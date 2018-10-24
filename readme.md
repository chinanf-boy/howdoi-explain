# gleitz/howdoi [![explain]][source] [![translate-svg]][translate-list]

<!-- [![size-img]][size] -->

[explain]: http://llever.com/explain.svg
[source]: https://github.com/chinanf-boy/Source-Explain
[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list
[size-img]: https://packagephobia.now.sh/badge?p=Name
[size]: https://packagephobia.now.sh/result?p=Name

「 即时编码, 通过命令行回答 」

---

## explain ✅

<!-- doc-templite START generated -->
<!-- time = '2018-08-12' -->
<!-- name = 'gleitz' -->
<!-- repo = 'howdoi' -->
<!-- commit = 'f0d9f54b3c7c1879eeb6ecfcc5193ceeb44ad0f2' -->
版本 | 与日期 | 最新更新 | 更多
---|---|---|---
[commit] | ⏰ 2018-08-12 | ![version] | [源码解释][source]

[commit]: https://github.com/gleitz/howdoi/tree/f0d9f54b3c7c1879eeb6ecfcc5193ceeb44ad0f2
[version]: https://img.shields.io/npm/v/howdoi.svg

<!-- doc-templite END generated -->

### 项目 中文 readme

[Readme](zh.md)

## 生活

[help me live , live need money 💰](https://github.com/chinanf-boy/live-need-money)

---

## 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [requirements.txt](#requirementstxt)
- [setup.py](#setuppy)
- [howdoi工作流程](#howdoi%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)
  - [howdoi/__init__.py](#howdoi__init__py)
  - [howdoi/howdoi.py](#howdoihowdoipy)
    - [python + import](#python--import)
    - [控制不同Py版本的导入](#%E6%8E%A7%E5%88%B6%E4%B8%8D%E5%90%8Cpy%E7%89%88%E6%9C%AC%E7%9A%84%E5%AF%BC%E5%85%A5)
    - [各种常量设置和获取](#%E5%90%84%E7%A7%8D%E5%B8%B8%E9%87%8F%E8%AE%BE%E7%BD%AE%E5%92%8C%E8%8E%B7%E5%8F%96)
    - [拿到requests会话](#%E6%8B%BF%E5%88%B0requests%E4%BC%9A%E8%AF%9D)
    - [各种工具函数和逻辑函数](#%E5%90%84%E7%A7%8D%E5%B7%A5%E5%85%B7%E5%87%BD%E6%95%B0%E5%92%8C%E9%80%BB%E8%BE%91%E5%87%BD%E6%95%B0)
      - [get_proxies](#get_proxies)
      - [_get_result](#_get_result)
      - [_add_links_to_text](#_add_links_to_text)
      - [get_text](#get_text)
      - [_extract_links_from_bing](#_extract_links_from_bing)
      - [_extract_links_from_google](#_extract_links_from_google)
      - [_extract_links](#_extract_links)
      - [_get_search_url](#_get_search_url)
      - [_get_links](#_get_links)
      - [get_link_at_pos](#get_link_at_pos)
      - [_format_output](#_format_output)
      - [_is_question](#_is_question)
      - [_get_questions](#_get_questions)
      - [_get_answer](#_get_answer)
      - [_get_instructions](#_get_instructions)
      - [format_answer](#format_answer)
      - [_enable_cache](#_enable_cache)
      - [_clear_cache](#_clear_cache)
      - [howdoi](#howdoi)
      - [get_parser](#get_parser)
      - [command_line_runner](#command_line_runner)
    - [`__main__`](#__main__)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


## requirements.txt

依赖管理

``` txt
Pygments==2.2.0
argparse==1.4.0
cssselect==1.0.3
lxml==4.2.4
pyquery==1.4.0
requests==2.19.1
requests-cache==0.4.13
```

## setup.py

给予 pip 安装使用的脚本，初看较为麻烦，可看[requests主作者的setup.py指南](https://github.com/kennethreitz/setup.py)

## howdoi工作流程

说说工作流程, 

1. 用户运行命令行，带上参数 (主要是需要提问的关键字;如`format date bash`) , [__main__启动了](#__main__)
2. 然后[command_line_runner](#command_line_runner)开始[解析命令行参数](#get_parser), 并相应分流，主流程是[howdoi 函数](#howdoi)
3. howdoi 开始它的表演，[获得命令函数](#_get_instructions)启动

> 先开始[获得结果链接](#_get_links)

- 3.1> 组合所有常量和需要提问的关键字，将其[变为**Url**](#_get_search_url)
- 3.2> 使用requests请求库，[请求上述Url, 获得结果返回](#_get_result) {代理之类的设置略}
- 3.3> 接过上述的html字符串，交由**pyquery**转换操作，[提取](#_extract_links) 搜索引擎(google,bind...) 的每个结果链接
- 3.4> 至此, [获得结果链接](#_get_links)的操作完成，继续下一步
> 开始[获取问题](#_get_questions)
- 3.5> 检查和过滤每个结果链接[是否为解答链接](#_is_question)
- 3.6> 根据重要的用户参数，要多少答案，对链接进行以下处理
    - 3.6.1 [计算下第几个链接呢](#get_link_at_pos)
    - 3.6.2 [请求解答链接，解析html, 获得答案](#_get_answer)，放入答案集合 *
- 3.7> 答案集合经由[获得命令函数](#_get_instructions)，返回[howdoi](#howdoi)统筹，再返回
- 3.8> 最后回到[command_line_runner](#command_line_runner) 打印适应的输出结果


> **pyquery**，类Jquery语法操作 html

### howdoi/__init__.py

py项目引用的默认目录,初始文件

```py
__version__ = '1.1.13'
```

### howdoi/howdoi.py


#### python + import

```py
#!/usr/bin/env python

######################################################
#
# howdoi - instant coding answers via the command line
# written by Benjamin Gleitzman (gleitz@mit.edu)
# inspired by Rich Jones (rich@anomos.info)
#
######################################################

import argparse
import glob
import os
import random
import re
import requests
import requests_cache
import sys
from . import __version__

from pygments import highlight
from pygments.lexers import guess_lexer, get_lexer_by_name
from pygments.formatters.terminal import TerminalFormatter
from pygments.util import ClassNotFound

from pyquery import PyQuery as pq
from requests.exceptions import ConnectionError
from requests.exceptions import SSLError

```

#### 控制不同Py版本的导入

```py
# Handle imports for Python 2 and 3
if sys.version < '3':
    import codecs
    from urllib import quote as url_quote
    from urllib import getproxies

    # Handling Unicode: http://stackoverflow.com/a/6633040/305414
    def u(x):
        return codecs.unicode_escape_decode(x)[0]
else:
    from urllib.request import getproxies
    from urllib.parse import quote as url_quote

    def u(x):
        return x


```

#### 各种常量设置和获取

```py
if os.getenv('HOWDOI_DISABLE_SSL'):  # Set http instead of https
    SCHEME = 'http://'
    VERIFY_SSL_CERTIFICATE = False
else:
    SCHEME = 'https://'
    VERIFY_SSL_CERTIFICATE = True

URL = os.getenv('HOWDOI_URL') or 'stackoverflow.com'

USER_AGENTS = ('Mozilla/5.0 (Macintosh; Intel Mac OS X 10.7; rv:11.0) Gecko/20100101 Firefox/11.0',
               'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:22.0) Gecko/20100 101 Firefox/22.0',
               'Mozilla/5.0 (Windows NT 6.1; rv:11.0) Gecko/20100101 Firefox/11.0',
               ('Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_4) AppleWebKit/536.5 (KHTML, like Gecko) '
                'Chrome/19.0.1084.46 Safari/536.5'),
               ('Mozilla/5.0 (Windows; Windows NT 6.1) AppleWebKit/536.5 (KHTML, like Gecko) Chrome/19.0.1084.46'
                'Safari/536.5'), )
SEARCH_URLS = {
    'bing': SCHEME + 'www.bing.com/search?q=site:{0}%20{1}',
    'google': SCHEME + 'www.google.com/search?q=site:{0}%20{1}'
}
STAR_HEADER = u('\u2605')
ANSWER_HEADER = u('{2}  Answer from {0} {2}\n{1}')
NO_ANSWER_MSG = '< no answer given >'
XDG_CACHE_DIR = os.environ.get('XDG_CACHE_HOME',
                               os.path.join(os.path.expanduser('~'), '.cache'))
CACHE_DIR = os.path.join(XDG_CACHE_DIR, 'howdoi')
CACHE_FILE = os.path.join(CACHE_DIR, 'cache{0}'.format(
    sys.version_info[0] if sys.version_info[0] == 3 else ''))
```

#### 拿到requests会话

``` py
howdoi_session = requests.session()
```

#### 各种工具函数和逻辑函数

##### get_proxies

获取代理函数

``` py
def get_proxies():
    proxies = getproxies()
    filtered_proxies = {}
    for key, value in proxies.items():
        if key.startswith('http'):
            if not value.startswith('http'):
                filtered_proxies[key] = 'http://%s' % value
            else:
                filtered_proxies[key] = value
    return filtered_proxies

```

##### _get_result

Url请求函数

``` py
def _get_result(url):
    try:
        return howdoi_session.get(url, headers={'User-Agent': random.choice(USER_AGENTS)}, proxies=get_proxies(),
                                  verify=VERIFY_SSL_CERTIFICATE).text
    except requests.exceptions.SSLError as e:
        print('[ERROR] Encountered an SSL Error. Try using HTTP instead of '
              'HTTPS by setting the environment variable "HOWDOI_DISABLE_SSL".\n')
        raise e

```

##### _add_links_to_text

添加答案的源网址

``` py
def _add_links_to_text(element):
    hyperlinks = element.find('a')

    for hyperlink in hyperlinks:
        pquery_object = pq(hyperlink)
        href = hyperlink.attrib['href']
        copy = pquery_object.text()
        if (copy == href):
            replacement = copy
        else:
            replacement = "[{0}]({1})".format(copy, href)
        pquery_object.replace_with(replacement)
```

##### get_text

添加源网址，并答案字符串返回

``` py
def get_text(element):
    ''' return inner text in pyquery element '''
    _add_links_to_text(element)
    return element.text(squash_space=False) # 已经不对了，python3.6 没法使用
    # 直接: return " ".join(element.text().split())

```

> squash_space=False 是为了，去掉多余空格

##### _extract_links_from_bing

解析bing搜索的结果链接

``` py
def _extract_links_from_bing(html):
    html.remove_namespaces()
    return [a.attrib['href'] for a in html('.b_algo')('h2')('a')]

```

##### _extract_links_from_google

解析google搜索的结果链接

``` py
def _extract_links_from_google(html):
    return [a.attrib['href'] for a in html('.l')] or \
        [a.attrib['href'] for a in html('.r')('a')]

```

##### _extract_links

解析结果链接

``` py
def _extract_links(html, search_engine):
    if search_engine == 'bing':
        return _extract_links_from_bing(html)
    return _extract_links_from_google(html)

```

##### _get_search_url

获取搜索Url

``` py
def _get_search_url(search_engine):
    return SEARCH_URLS.get(search_engine, SEARCH_URLS['google'])

```

##### _get_links

获取，解析，搜索引擎「bing,google」的结果链接`<a href>`们

``` py
def _get_links(query):
    search_engine = os.getenv('HOWDOI_SEARCH_ENGINE', 'google')
    search_url = _get_search_url(search_engine)

    result = _get_result(search_url.format(URL, url_quote(query)))
    html = pq(result)
    return _extract_links(html, search_engine)

```

##### get_link_at_pos

用户要求的答案数量，到第几个解答链接了

``` py
def get_link_at_pos(links, position):
    if not links:
        return False

    if len(links) >= position:
        link = links[position - 1]
    else:
        link = links[-1]
    return link

```

##### _format_output

格式化输出，带不带颜色

``` py
def _format_output(code, args):
    if not args['color']:
        return code
    lexer = None

    # try to find a lexer using the StackOverflow tags
    # or the query arguments
    for keyword in args['query'].split() + args['tags']:
        try:
            lexer = get_lexer_by_name(keyword)
            break
        except ClassNotFound:
            pass

    # no lexer found above, use the guesser
    if not lexer:
        try:
            lexer = guess_lexer(code)
        except ClassNotFound:
            return code

    return highlight(code,
                     lexer,
                     TerminalFormatter(bg='dark'))

```

##### _is_question

是不是一个解答链接

``` py
def _is_question(link):
    return re.search('questions/\d+/', link)

```

##### _get_questions

过滤解答链接

``` py
def _get_questions(links):
    return [link for link in links if _is_question(link)]

```

##### _get_answer

从请求解答链接，返回的html中解析答案，组合

``` py
def _get_answer(args, links):
    link = get_link_at_pos(links, args['pos'])
    if not link:
        return False
    if args.get('link'):
        return link
    page = _get_result(link + '?answertab=votes') # 请求解答链接
    html = pq(page) # 解析html

    first_answer = html('.answer').eq(0) 

    instructions = first_answer.find('pre') or first_answer.find('code')
    args['tags'] = [t.text for t in html('.post-tag')]

    if not instructions and not args['all']:
        text = get_text(first_answer.find('.post-text').eq(0))
    elif args['all']:
        texts = []
        for html_tag in first_answer.items('.post-text > *'):
            current_text = get_text(html_tag)
            if current_text:
                if html_tag[0].tag in ['pre', 'code']:
                    texts.append(_format_output(current_text, args))
                else:
                    texts.append(current_text)
        text = '\n'.join(texts)
    else:
        text = _format_output(get_text(instructions.eq(0)), args)
    if text is None:
        text = NO_ANSWER_MSG
    text = text.strip()
    return text # 获得答案字符串

```

##### _get_instructions

内置问题，解答函数

``` py
def _get_instructions(args):
    links = _get_links(args['query'])
    if not links:
        return False

    question_links = _get_questions(links)
    if not question_links:
        return False

    only_hyperlinks = args.get('link') # 用户参数， 仅需要解答链接
    star_headers = (args['num_answers'] > 1 or args['all'])

    answers = []
    initial_position = args['pos']
    spliter_length = 80
    answer_spliter = '\n' + '=' * spliter_length + '\n\n'

    for answer_number in range(args['num_answers']): # 用户要几个答案
        current_position = answer_number + initial_position
        args['pos'] = current_position
        link = get_link_at_pos(question_links, current_position)
        answer = _get_answer(args, question_links)
        if not answer:
            continue
        if not only_hyperlinks: # 是否仅输出解答链接
            answer = format_answer(link, answer, star_headers)
        answer += '\n'
        answers.append(answer) # 答案集合
    return answer_spliter.join(answers)

```

##### format_answer

格式化答案

``` py
def format_answer(link, answer, star_headers):
    if star_headers:
        return ANSWER_HEADER.format(link, answer, STAR_HEADER)
    return answer

```

##### _enable_cache

缓存缓存

``` py
def _enable_cache():
    if not os.path.exists(CACHE_DIR):
        os.makedirs(CACHE_DIR)
    requests_cache.install_cache(CACHE_FILE)

```

##### _clear_cache

删除缓存

``` py
def _clear_cache():
    for cache in glob.iglob('{0}*'.format(CACHE_FILE)):
        os.remove(cache)

```

##### howdoi

开始，获取解答命令，和一些异常输出

``` py
def howdoi(args):
    args['query'] = ' '.join(args['query']).replace('?', '')
    try:
        return _get_instructions(args) or 'Sorry, couldn\'t find any help with that topic\n'
    except (ConnectionError, SSLError):
        return 'Failed to establish network connection\n'

```

##### get_parser

命令行参数解析

``` py
def get_parser():
    parser = argparse.ArgumentParser(description='instant coding answers via the command line')
    parser.add_argument('query', metavar='QUERY', type=str, nargs='*',
                        help='the question to answer')
    parser.add_argument('-p', '--pos', help='select answer in specified position (default: 1)', default=1, type=int)
    parser.add_argument('-a', '--all', help='display the full text of the answer',
                        action='store_true')
    parser.add_argument('-l', '--link', help='display only the answer link',
                        action='store_true')
    parser.add_argument('-c', '--color', help='enable colorized output',
                        action='store_true')
    parser.add_argument('-n', '--num-answers', help='number of answers to return', default=1, type=int)
    parser.add_argument('-C', '--clear-cache', help='clear the cache',
                        action='store_true')
    parser.add_argument('-v', '--version', help='displays the current version of howdoi',
                        action='store_true')
    return parser

```

##### command_line_runner

有始有终的命令运行器

``` py
def command_line_runner():
    parser = get_parser() # 始
    args = vars(parser.parse_args())

    if args['version']:
        print(__version__)
        return

    if args['clear_cache']:
        _clear_cache()
        print('Cache cleared successfully')
        return

    if not args['query']:
        parser.print_help()
        return

    # enable the cache if user doesn't want it to be disabled
    if not os.getenv('HOWDOI_DISABLE_CACHE'):
        _enable_cache()

    if os.getenv('HOWDOI_COLORIZE'):
        args['color'] = True

    utf8_result = howdoi(args).encode('utf-8', 'ignore')
    if sys.version < '3':
        print(utf8_result)
    else:
        # Write UTF-8 to stdout: https://stackoverflow.com/a/3603160
        sys.stdout.buffer.write(utf8_result)
    # close the session to release connection
    howdoi_session.close() # 终
```

#### `__main__`

此文件作为直接运行时，触发

``` py
if __name__ == '__main__':
    command_line_runner()
```