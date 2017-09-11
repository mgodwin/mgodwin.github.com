---
layout: post
title: Run rails tests in atom
---

I've been trying out the [atom](https://www.atom.io) editor for a few weeks now, and I previously
used the sublime build tooling to run my rails tests.  It was pretty nice, because
you could hit `cmd + b` and sublime would auto run your tests if you had a build
file included in your project directory.  There is a corresponding atom package called [atom-build](https://atom.io/packages/build)
that operates pretty much the same way as sublime.  If you get it installed, you can
use the atom-build file below to have the package run your rails 5 tests:

```yaml
# .atom-build.yml
cmd: "bin/rails test {FILE_ACTIVE}:{FILE_ACTIVE_CURSOR_ROW}"
name: Rails - Run Test
atomCommandName: 'rails-test:single-test'
cwd: "{PROJECT_PATH}"
errorMatch:
  - \[(?<file>.+):(?<line>\d+)\]:\s*(?<message>.*)
targets:
  Rails - Test File:
    cmd: "bin/rails test {FILE_ACTIVE}"
    cwd: "{PROJECT_PATH}"
    atomCommandName: 'rails-test:current-file'
    errorMatch:
      - \[(?<file>.+):(?<line>\d+)\]:\s*(?<message>.*)
  Rails - Test Project:
    cmd: "bin/rails test"
    atomCommandName: 'rails-test:all-tests'
    cwd: "{PROJECT_PATH}"
    errorMatch:
      - \[(?<file>.+):(?<line>\d+)\]:\s*(?<message>.*)
```

The atom-build package is actually a couple steps ahead of the sublime counterpart,
because it'll automatically highlight lines that had errors _and_ you can run an
individual test, not just an individual file :metal:.
