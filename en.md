howdoi
======

[![image]]

instant coding answers via the command line
-------------------------------------------

[![image][1]]

Are you a hack programmer? Do you find yourself constantly Googling for
how to do basic programming tasks?

Suppose you want to know how to format a date in bash. Why open your
browser and read through blogs (risking major distraction) when you can
simply stay in the console and ask howdoi:

    $ howdoi format date bash
    > DATE=`date +%Y-%m-%d`

howdoi will answer all sorts of queries:

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

Installation
------------

    pip install howdoi

or

    pip install git+https://github.com/gleitz/howdoi.git#egg=howdoi

or

    brew install https://raw.github.com/gleitz/howdoi/master/howdoi.rb

or

    python setup.py install

Usage
-----

    usage: howdoi.py [-h] [-p POS] [-a] [-l] [-c] [-n NUM_ANSWERS] [-C] [-v] QUERY [QUERY ...]

    instant coding answers via the command line

    positional arguments:
      QUERY                 the question to answer

    optional arguments:
      -h, --help            show this help message and exit
      -p POS, --pos POS     select answer in specified position (default: 1)
      -a, --all             display the full text of the answer
      -l, --link            display only the answer link
      -c, --color           enable colorized output
      -n NUM_ANSWERS, --num-answers NUM_ANSWERS
                            number of answers to return
      -C, --clear-cache     clear the cache
      -v, --version         displays the current version of howdoi

As a shortcut, if you commonly use the same parameters each time and
don\'t want to type them, add something similar to your .bash\_profile
(or otherwise). This example gives you 5 colored results each time.

    alias h='function hdi(){ howdoi $* -c -n 5; }; hdi'

And then to run it from the command line simply type:

    $h this is my query for howdoi

Author
------

-   Benjamin Gleitzman ([\@gleitz])

Notes
-----

-   Works with Python2 and Python3
-   A standalone Windows executable with

  [image]: http://imgs.xkcd.com/comics/tar.png
  [![image]]: https://xkcd.com/1168/
  [1]: https://secure.travis-ci.org/gleitz/howdoi.png?branch=master
  [![image][1]]: https://travis-ci.org/gleitz/howdoi
  [\@gleitz]: http://twitter.com/gleitz