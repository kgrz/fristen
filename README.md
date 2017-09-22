Fristen
-------


👾 I don't remember what the name even means


This is an ansible playbook that sets up a Git-driven deployment style
Jekyll instance on a fresh linux machine. I use similar workflow to
deploy the rare blog post gems of knowledge I tend to write.

These are the things that the playbook sets up:

1. Nginx as the server
1. fail2ban with some [custom rules][fail2ban-customrules]
1. Ruby-Install
1. Chruby
1. Ruby (hard-coded to 2.4.1), customisable via a
   [variable][rubyversion-file]
1. Bundler
1. A bare Git repository on the remote that has a Git hook. This is used
   to listen to Git pushes, and then clone the blog from GitHub, and
   build the application


I haven't made this terribly configurable, but you can try out modifying
the variables and stuff to customise it to suit your personal needs.

#### Linux compatibility

I've tested this on Debian Jessie, and Ubuntu 14. Ubuntu latest (I think
Xenial is the one I tried) doesn't ship with Python 2.7, and only has a
`python3` binary, which made ansible crash, and I didn't investigate
much because I don't intend to use it.

#### Playbook Layout

All the individual steps are separated into ansible roles, which have a
particular layout structure. Each role has a `tasks` folder, which is
the most important one, and optionally `vars`, `templates`, `files`.


```
roles
├── chruby
│   ├── tasks
│   │   └── main.yml
│   └── vars
│       └── main.yml
├── common
│   └── tasks
│       └── main.yml
├── fail2ban
│   ├── files
│   │   ├── fail2ban.conf
│   │   ├── nginx-login.conf
│   │   ├── nginx-noscript.conf
│   │   └── nginx-proxy.conf
│   └── tasks
│       └── main.yml
├── git-hook
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   │   └── post-receive
│   └── vars
│       └── main.yml
├── nginx
│   ├── handlers
│   │   └── main.yml
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   │   └── webserver.conf
│   └── vars
│       └── main.yml
├── ruby
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   │   └── ruby-version
│   └── vars
│       └── main.yml
└── ruby-install
    ├── tasks
    │   └── main.yml
    └── vars
        └── main.yml
```




#### Might do in the future

1. Add a way to output all the exact git remotes properly to stdout.
1. More conservative installations. Currently, Ruby is installed even if
   present. That should be done only if ruby is not present. Same goes
   for `chruby`, `ruby-install` etc.
1. Cleanup unneeded user-creation
1. May be avoid installing Ruby dependency managers.
