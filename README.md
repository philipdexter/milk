# Milk

A modernized porcelain for git.

## Why

Apparently terminals are going through some sort of modern renaissance where
old, antiquated tools are being replaced with hip, fresh tools. I'm hopping on
the bandwagon.

See:

* [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep)
* [jakubroztocil/httpie](https://github.com/jakubroztocil/httpie)
* [ogham/exa](https://github.com/ogham/exa)
* [sharkdp/bat](https://github.com/sharkdp/bat)
* [sharkdp/fd](https://github.com/sharkdp/fd)


Anyway, I've always found git's standard porcelain to be... confusing. I'm sure
many newer users may also feel that way, so I'd like to see if I can do
something about it. Maybe I can't. Whatever.

The original idea credit goes to [eevee/tcup](https://github.com/eevee/tcup),
but that project seems to be have been long abandoned. Many moons ago it was
even written in Rust 0.6.

### Why is it called that

Because short words are good for CLI tools and the phrase 'git milk?' was
funny to me. Whatever.

## Design

I don't have a really solid vision aside from "easier to use", but here are
some areas I've noted in git's existing porcelain:

* Many commands are extremely overloaded, which leads to some context-sensitive
  ambiguity. These commands can often do unexpected things or are intended to
  do things completely different from the way they're actually used. Notably,
  I've been using `git reset` as a way to unstage all changes, or unstage
  changes for a particular file via `git reset <path>`. I recently learned
  `git reset` *actually* sets the `HEAD` to a specific revision and sets the
  index to the state of that revision. Effectively, this *does* unstage
  things... but it also does a lot more than that, which is rather unintuitive.
  Also, it has some odd ambiguous cases for arguments - `git reset <tree-ish>`
  and `git reset <path>`. If a user ever tries to use an ambiguous command, it
  will notify them - but it's still surprising. I don't like being surprised by
  my tooling.
* `git checkout` is similarly overloaded. Also, both of these don't really have
  names that represent what I actually want to do. `reset` is actually used to
  *unstage*, and `checkout` is used to both *switch branches* and to *undo
  local changes*. Weird stuff.
* `git branch` is super overloaded. Enumerating, creating, renaming, moving,
  deleting, and copying are all the same command, just with a different flag.
  I'd like to normalize this with subcommands instead of flags, eg:
  * `git-branch -v` -> `milk branch ls`
  * `git-branch <branch>` -> `milk branch new <branch>`
  * `git branch -m <old-name> <new-name>` -> `milk branch rename <old> <new>`
  * `git-branch -f <branch> <commit-ish>` -> `milk branch mv <branch> <commit-ish>`
  * `git branch -d <branch>` -> `milk branch rm <branch>`
* `git stash` is also fairly overloaded, but with subcommands instead. That's
  weird, but I guess it makes sense sometimes? I don't have many ideas to fix
  this one.
* `git add .` and `git commit -a` make it too easy to intentionally add garbage
  that shouldn't be tracked. `git add -p` is excellent, but it makes the
  process of committing a bit more of a chore. How can this be streamlined so
  users are encouraged to make small, logical commits instead of large, lumpy
  commits?
* `git commit -m` encourages users to write short, unhelpful messages. It's
  GONE.
* `commit-ish`, `tree-ish`, and their ilk are sometimes ambiguous and
  confusing. I'd like to create some notation to clearly indicate what an
  entity is expected to be, for example `beef` may refer to commit, `@beef` may
  refer to a branch, and `#beef` may refer to a tag. Dunno yet.
* Probably other stuff too. Dunno.
