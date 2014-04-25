---
layout: post
title: merging sub­version repo­sitories
date: '2010-02-16T18:48:00+01:00'
tags:
- subversion
- vcs
tumblr_url: http://saji-codes.tumblr.com/post/393051138
---

Some time ago I tried to create single subversion repository for a project that was spread across several of these.

- In the old days we used `FOO1` repository.
  After several months we learned about standard layout and moved the code to `trunk/` subdirectory.
- Another several months and project has been rewritten and new repository `FOO2` has been created.
- Again, another repository emerged (`FOO2.1`), this time we even created several branches in it.
- In addition copy of the code has been living in some subproject repository (`BAR`).

Here’s a recipe to merge these, if you happen to be in similar situation.


### Create yet another svn repository

```sh
svnadmin create /home/svn/FOO
# set up permissions to it etc.
cp -v /home/FOO2.1/conf/{svnserve.conf,passwd} /home/FOO
# create standard layout
svn mkdir --parents file:///home/svn/FOO/{trunk,tags,branches} -m 'layout'
```


### Dump old repositories

```sh
svnadmin dump /home/svn/FOO1 > old-FOO1.dump
svnadmin dump /home/svn/FOO2 > old-FOO2.dump
svnadmin dump /home/svn/FOO2.1 > old-FOO2.1.dump
svnadmin dump /home/svn/BAR > old-BAR.dump
```


### Take only what you need

```sh
# only trunk
cat old-FOO1.dump | svndumpfilter --renumber-revs --drop-empty-revs \
    include trunk > new-FOO1.dump
cat old-FOO2.dump | svndumpfilter --renumber-revs --drop-empty-revs \
    include trunk > new-FOO2.dump
# you can specify more than one path
cat old-FOO2.1.dump | svndumpfilter --renumber-revs --drop-empty-revs \
    include trunk branches > new-FOO2.1.dump
# only one subdirectory from trunk containing FOO project
cat old-BAR.dump | svndumpfilter --renumber-revs --drop-empty-revs \
    include trunk/FOO/ > new-BAR.dump
```


### Fix paths

At this point you have several dumpfiles and probably all of them would like to put files in `trunk/`, which is not good. So, we have to translate paths.

```sh
sed -ir \
    -e 's,^Node-([^\s]*)path: trunk,Node-\1path: branches/1.0,' \
    -e 's,^Node-path: trunk$,Node-path: dummy1,' \
    -e 's,^Node-([^\s]*)path: (lib|tpl|htdocs|LICENSE|README),New-\1path: branches/1.0-legacy/\2,' \
    new-FOO1.dump
```

This will translate all `trunk/` paths to `branch/1.0/`. Also, it searches where it is referred to without any subdirectory (basically, creation of a directory) and translates into dummy value. This is to avoid conflicts (every single dumpfile will try to create these directories — translate them to `dummy1`, `dummy2` etc).

The last expression (`-e`) sorts out the dark times, when we did not use standard subversion layout and translates all paths to `branches/1.0-legacy` — you have to specify all files that were in any revision in root directory (even when they were deleted before moving things to `trunk/`).

After [sed(1)]ing you can check what paths are mentioned in new dump:

```sh
egrep -a '^Node[a-z-]*-path:' new-FOO1.dump \
    | cut -d'/' -f-2 | sort | uniq
# to check two levels of directories (here: list subdirectories of trunk/)
```


### Fix paths in rest of the dumps

```sh
sed -ir \
    -e 's,^Node-([^\s]*)path: trunk,Node-\1path: branches/2.0,' \
    -e 's,^Node-path: trunk$,Node-path: dummy2,' \
    new-FOO2.dump

# trunk does not get translated here, as it is to stay trunk in new repository
sed -ir \
    -e 's,^Node-path: trunk$,Node-path: dummy2,' \
    -e 's,^Node-([^\s]*)path: branches/stable,Node-\1path: branches/2.1-stable,' \
    -e 's,^Node-([^\s]*)path: branches/xxx,Node-\1path: branches/2.1-xxx,' \
    -e 's,^Node-path: branches$,Node-path: dummy3,' \
    new-FOO2.1.dump

sed -ir \
    -e 's,^Node-([^\s]*)path: trunk/FOO,Node-\1path: branches/2.1-bar,' \
    new-BAR.dump
```

Remember to check dumps with that [grep(1)] command above.


### load ‘em up!

Now you should be ready to load all dumpfiles into the new repository.

```sh
cat new-FOO1.dump | svnadmin load /home/svn/FOO
cat new-FOO2.dump | svnadmin load /home/svn/FOO
cat new-FOO2.1.dump | svnadmin load /home/svn/FOO
cat new-BAR.dump | svnadmin load /home/svn/FOO
```

Unfortunately revisions will created in order of loading dumpfiles, not sorted by date, so you might want to tweak loading order.

You might want to check if everything is going according to plan after every load

```sh
svn ls file:///home/svn/FOO/
```

Also, be warned that if any error occur, you have to re-create the repository and start loading from the first one. So if dumpfiles are big, or you are afraid that something may go bad with one of them — backup `/home/svn/FOO` after every successful load, so you may tweak dumpfile that causes problems and load it again.


### one-liner

Of course, if you are sure that everything is going to work, you may dump and load with one, smooth command:

```sh
svnadmin dump /home/svn/FOO2.1 \
    | svndumpfilter --renumber-revs --drop-empty-revs \
        include trunk branches \
    | sed -ir \
        -e 's,^Node-path: trunk$,Node-path: dummy2,' \
        -e 's,^Node-([^\s]*)path: branches/stable,Node-\1path: branches/2.1-stable,' \
        -e 's,^Node-([^\s]*)path: branches/xxx,Node-\1path: branches/2.1-xxx,' \
        -e 's,^Node-path: branches$,Node-path: dummy3,' \
    | svnadmin load /home/svn/FOO
```

### kill the dummies

```sh
svn rm /home/svn/FOO/dummy{1,2,3} -m 'repository merged' # we won't be needing these anymore
```


### ta-dah

You are now proud owner of one, ultimate repository containing all project code. It is far from being perfect. To be so, you’d have to tweak dumpfiles to remove dummies completely and change some `file-creation` to `file-copy` operations; and somehow sort revisions by date.

- - -

Now, there might be good time to consider migration from SVN to GIT. [;



[grep(1)]: http://linux.die.net/man/1/grep
[sed(1)]: http://linux.die.net/man/1/sed
