@[TOC]
## 引言
大家好，我是jony。

最近在工作中写一个批处理脚本，令人抓狂的是每次都不知道脚本要跑到啥时候结束，于是想到给程序添加个进度条。

逛了一圈，没找到特别趁手的轮子，本着有手就行的原则，今天简单地给大家撸一个终端进度条。
## 原理
终端进度条打印的原理是通过输入`\r`将光标位置移动到当前行的行首，重新打印一份进度信息。

如果是使用`\n`，则光标会另起一行打印信息。

## 上才艺
首先从核心功能出发，进度条要告诉我的信息有
- 一共要完成多少任务
- 现在完成了多少任务
- 到什么时候才能完成全部任务

根据上面的需求，画了个大概的样子长这样
`[█████████████████████████]100/100 [eta]16:33:39`

抽象的用户调用函数有3个

`New()`新建进度条实例 
`Done()`推进进度条进展 
`Finish()`完成进度条

是不是和sync.WaitGroup很像。

调用代码大概长这样：
```go
func main() {
	bar := progress.New(100)
	for i := 0; i < 100; i++ {
		time.Sleep(time.Second / 10)
		bar.Done(1)
	}
	bar.Finish()
}
```

所以根据用户调用需求，首先定义进度条结构体。
```go
type Bar struct {
	total         int64         // 总进度
	current       int64         // 当前进度
	filler        string        // 进度填充字符
	filler_length int64         // 进度条长度
	time_format   string        // 进度条时间格式
	interval      time.Duration // 打印时间间隔
	begin         time.Time     // 任务开始时间
}
```
然后根据用户调用的函数，给出函数实现，当然这里面加了一些函数参数可选项。

可以在初始化实例的时候自定义一些元素，比如填充字符，比如时间格式或者是每隔多少时间刷新一次进度条等等。
```go
// New 新建进度条实例
func New(total int64, opts ...func(*Bar)) *Bar {
	bar := &Bar{
		total:         total,
		filler:        "█",
		filler_length: 25,
		time_format:   "15:04:05", // 2006-01-02T15:04:05
		interval:      time.Second,
		begin:         time.Now(),
	}
	for _, opt := range opts {
		opt(bar)
	}
	// 定时打印
	ticker := time.NewTicker(bar.interval)
	go func() {
		for bar.current < bar.total {
			fmt.Print(bar.get_progress_string())// 打印进度
			<-ticker.C
		}
	}()
	return bar
}
// Done 更新完成进度
func (bar *Bar) Done(i int64) {
	bar.current += i
}
// Finish 完成最后进度条
func (bar *Bar) Finish() {
	fmt.Println(bar.get_progress_string())
}
// WithFiller 设置进度条填充字符
func WithFiller(filler string) func(*Bar) {
	return func(bar *Bar) {
		if len(bar.filler) != 0 {
			bar.filler = filler
		}
	}
}
```
那么处理完了用户怎么使用之后，我们就来开始处理怎么给用户展示进度条效果。

要想根据进度填充不同的字符比例，先算进度百分比，长下面这样子。
```go
//get_percent 获取进度百分比,区间0-100
func (bar *Bar) get_percent() int64 {
	return bar.current * 100 / bar.total
}
```
因为我们进度条并不需要那么精确，所有这里都用的是整数来处理，更方便一些，不用做各种类型转换。

那么拿到百分比之后，就能根据进度条总长度来计算要填充多少个`█`。

接下来算任务什么时候完成，这里用的算法是，用当前完成了多少个任务和花了多少时间来估算总任务数的要花费多少时间，得到预计什么时候完成，代码是这样子的：

```go
//get_eta 获取eta时间
func (bar *Bar) get_eta(now time.Time) string {
	eta := (now.Unix() - bar.begin.Unix()) * 100 / (bar.get_percent() + 1)
	return bar.begin.Add(time.Second * time.Duration(eta)).Format(bar.time_format)
}
```
最后，我们来处理下需要在控制台打印的字符串，同时作为非核心需求，我们还想看批处理操作的速度，所以这里用`QPS`来表达我们整个任务处理的速度。
```go
//get_progress_string 获取打印控制台字符串
func (bar *Bar) get_progress_string() string {
	fills := bar.get_percent() * bar.filler_length / 100
	for i := int64(0); i < bar.filler_length; i++ {
		switch {
		case i < fills:
			chunks[i] = bar.filler
		default:
			chunks[i] = " "
		}
	}
	now := time.Now()
	eta := bar.get_eta(now)
	qps := bar.current / (now.Unix() - bar.begin.Unix() + 1)
	return fmt.Sprintf("\r[%s]%d/%d [eta]%s [qps]%d ", strings.Join(chunks, ""), bar.current, bar.total, eta, qps)
}
```
最终呈现的效果
`[█████████████████████████]100/100 [eta]16:33:39 [qps]9 `

当然，为了更酷炫一点，同时还引入了emoji字符，能够根据字符自适应地调整显示效果。

![在这里插入图片描述](https://img-blog.csdnimg.cn/587bdcb8d2984db1a9d1b36abb7397b6.png)

下面是项目github地址，供大家参考
```shell
https://github.com/jony-lee/go-progress-bar
```
## 知识点总结
下面是知识点总结

- 使用`\r`来将控制台光标定位到行首实现行内进度条刷新。
- 使用函数可选参数来实现用户自定义设置。
- 使用函数`time.NewTicker()`实现定时刷新控制台进度条。
