
# howdoi

![image]

## 即时编码通过命令行回答

![image][1]

你是一个hack程序员吗? 你是否经常谷歌搜索如何做基本的编程任务?

假设您想知道如何在 `bash` 中格式化日期.打开你的浏览器，并通过博客阅读(注入大大的注意力)，为什么不只是呆在控制台，并询问 howdoi 呢,

```
$ howdoi format date bash
> DATE=`date +%Y-%m-%d`
```

howdoi 将回答各种问题:

```
$ howdoi print stack trace python
> import traceback
>
> try:
>     1/0
> except:
>     print '>>> traceback <<<'
>     traceback.print_exc()
>     print '>>> end of traceback <<<'
> traceback.print_exc()

$ howdoi convert mp4 to animated gif
> video=/path/to/video.avi
> outdir=/path/to/output.gif
> mplayer "$video" \
>         -ao null \
>         -ss "00:01:00" \  # starting point
>         -endpos 10 \ # duration in second
>         -vo gif89a:fps=13:output=$outdir \
>         -vf scale=240:180

$ howdoi create tar archive
> tar -cf backup.tar --exclude "www/subf3" www
```

## 安装

```
pip install howdoi
```

要么

```
pip install git+https://github.com/gleitz/howdoi.git#egg=howdoi
```

要么

```
brew install https://raw.github.com/gleitz/howdoi/master/howdoi.rb
```

要么

```
python setup.py install
```

## 用法

```
usage: howdoi.py [-h] [-p POS] [-a] [-l] [-c] [-n NUM_ANSWERS] [-C] [-v] QUERY [QUERY ...]

instant coding answers via the command line

positional arguments:
  QUERY                 the question to answer

optional arguments:
  -h, --help            显示此帮助消息并退出
  -p POS, --pos POS     在指定位置选择答案（默认值：1）
  -a, --all             显示答案的全文
  -l, --link            仅显示答案链接
  -c, --color           启用彩色输出
  -n NUM_ANSWERS, --num-answers NUM_ANSWERS
                        返回的答案数量
  -C, --clear-cache     清除缓存
  -v, --version         显示当前版本的howdoi
```

作为一种快捷方式,如果您每次都经常使用相同的参数，并且不想每次都输入它们,请添加类似于`.bash_profile`(或其他). 如下面示例的，每次为您提供 5 个彩色结果.

```
alias h='function hdi(){ howdoi $* -c -n 5; }; hdi'
```

然后从命令行运行它只需键入:

```
$h this is my query for howdoi
```

## 作者

- Benjamin Gleitzman ([\@gleitz])

## 描述

- 适用于 Python2 和 Python3
- 一个独立的 Windows 可执行文件

[image]: http://imgs.xkcd.com/comics/tar.png
[1]: https://secure.travis-ci.org/gleitz/howdoi.png?branch=master
[\@gleitz]: http://twitter.com/gleitz
