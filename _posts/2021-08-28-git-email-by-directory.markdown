---
title: "Set up different work e–mail in git by directory"
tweet: "New blog note: How to set up work e-mail for git repos checked out in specific directory"
---

For some time now git can include additional config files depending on
directory you are in (since [version 2.13.0][release-includeIf] to be
precise). Most common use case for this is setting your work e–mail for
repositories checked out in one directory
(<acronym lang="la" title="exempli gratia">e.g.</acronym> `~/src/@example-inc`)
while keeping your private e–mail for the rest of the projects.

There’s a good chance you currently have something similar to this in
your `~/.gitconfig`:

```ini
[user]
    name = "Test “The Face” McTestface"
    email = test@example.net
    # Hopefully you also have a GPG key for the e–mail.
    # Ignore signingKey key here and later if you don’t.
    signingKey = "0123456789ABCDEF"
```

If you want to use different e–mail for work stuff, you need to remember
to set then every time you create or clone a repository:

```sh
git config user.name "Test McTestface"
git config user.email "test.mctestface@example.com"
git config user.signingKey "FEDCBA9876543210"
```

_No more!_

We’ll need to add a [conditional include] to our
`~/.gitconfig`:

```ini
[user]
    name = "Test “The Face” McTestface"
    email = test@example.net
    signingKey = "0123456789ABCDEF"

[includeIf "gitdir:~/src/@example-inc"]
    path = ./.gitconfig.work
```

Then we need to create `~/.gitconfig.work` and put any config you want
to be used for repositories checked out in `~/src/@example-inc` (or
it’s subdirectories):

```ini
[user]
    name = "Test McTestface"
    email = "test.mctestface@example.com"
    signingKey = "FEDCBA9876543210"
```

Presto!

---

If you are using vim, you may want to add a [vim modeline] to get proper
syntax highlighting in `~/.gitconfig.work`:

```ini
# vim: ft=gitconfig
```


[release-includeIf]: https://github.com/git/git/blob/master/Documentation/RelNotes/2.13.0.txt#L127-L130
[conditional include]: https://git-scm.com/docs/git-config#_conditional_includes
[vim modeline]: https://vimhelp.org/options.txt.html#modeline
