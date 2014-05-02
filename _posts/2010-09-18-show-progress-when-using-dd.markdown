---
layout: post
title: show progress when using dd(1)
date: '2010-09-18T13:47:25+02:00'
tags:
- shell
tumblr_url: http://saji-codes.tumblr.com/post/1142783813
---
<ins class=block>
bar(1) can help you display better progress information
including percent done and estimated remaining time.
<a href=#update-1>See updated function</a>.
</ins>

[dd(1)] is a tool that you can use for, for example, backup the whole disk partition. Unfortunatelly it does not show progress, so you don’t really know how long will it take.

On the other hand, yes you can. Just add this to your `.bashrc`:

```sh
ddv ()
{ 
    dd "$@" & pid=$!
    printf "dd(1) forked, pid=%d\n\n" $pid
    while test -e /proc/$pid; do
        sleep 3
        kill -USR1 $pid
    done
}
```


Voila, this will send `dd` process in the background and send a `USR1` signal to it every 3s, which makes it display the progress.


---

<a id=update-1></a>

### Update: using bar(1)

Having [bar(1)], you can render slick progress bar with estimated finish
time and all.

```sh
ddv ()
{
    if which bar >/dev/null 2>&1
    then

        # bar(1) is available, us it for fancy progress bar

        # Extract input and output paths from arguments
        INPUT=
        OUTPUT=
        for (( argidx = 0 ; argidx <= $# ; argidx++ ))
        do
            A="$( echo "${@:$argidx:1}" | cut -d= -f1 )"
            B="$( echo "${@:$argidx:1}" | cut -d= -f2- )"
            if [ "$A" = "if" ]
            then
                INPUT="$B"
            elif [ "$A" = "of" ]
            then
                OUTPUT="$B"
                argprevidx=$(( argidx - 1 ))
                argnextidx=$(( argidx + 1 ))
            fi
        done

        if [ -z "$OUTPUT" ]
        then
            echo "No of= parameter given" 1>&2
            return 64
        fi

        # Remove "of=$OUTPUT" from $@, we'll redirect output directly to it
        set -- ${@:1:$argprevidx} ${@:$argnextidx}

        if ! [ -e "$INPUT" ]
        then
            echo "Input file does not exist:" "$INPUT" 1>&2
            return 66
        elif ! [ -r "$INPUT" ]
        then
            echo "Input file is not readable:" "$INPUT" 1>&2
            return 66
        fi

        # Determine input size for showing progress
        SIZE=
        if [ -f "$INPUT" ]
        then
            SIZE="$( stat --format=%s "$INPUT" )"
        elif [ -b "$INPUT" ]
        then
            SIZE="$( blockdev --getsize64 "$INPUT" )"
        fi

        if [ -z "$SIZE" ]
        then
            echo "Failed to get size of $1" 1>&2
            return 65
        fi

        dd "$@" | bar --size "$SIZE" > "$OUTPUT"

    else

        # No bar(1) available — fall back to sending USR1 signal to
        # dd(1) every 3s, which makes it show I/O statistics

        dd "$@" &
        pid=$!
        printf "dd(1) forked, pid=%d\n\n" $pid
        while test -e /proc/$pid ; do
            sleep 3
            kill -USR1 $pid
        done

    fi
}
```



[dd(1)]: http://linux.die.net/man/1/dd
[bar(1)]: http://linux.die.net/man/1/bar
