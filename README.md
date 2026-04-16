# salt-grains-environment

My solution to managing what environment my computers are in.

The folder structure here mimics how the file should sit in the [file_root](http://docs.saltstack.com/ref/file_server/file_roots.html).  For example, the *_grains* folder is same *_grains* folder referenced in the [grains doc](http://docs.saltstack.com/topics/targeting/grains.html?highlight=_grains#writing-grains).

## Usage

Using this is pretty straight forward.  In my environment, the `prd` environment is the default environment (Note: `base` still exists, but is considere global).  We have two other environments:

* `dev`
* `pre`
 
The `dev` environment houses development/test machines that may be working in a broken state at any given time.  The computer in the `pre` environment are our guinea pigs -- where we test what we believe is a working config before releasing to production (`prd`).

In order to have this work in your environment, you will first need to configure your [file_roots](http://docs.saltstack.com/ref/configuration/master.html#file-roots) ([more info](http://docs.saltstack.com/ref/file_server/file_roots.html#environments)) and [pillar_roots](http://docs.saltstack.com/ref/configuration/master.html#pillar-roots) in your master config file.  Here's mine, as an example:
```yaml
file_roots:
  base:
    - /srv/salt
    - /srv/salt/prd
  dev:
    - /srv/salt/dev
    - /srv/salt/pre
    - /srv/salt/prd
  pre:
    - /srv/salt/pre
    - /srv/salt/prd
  prd:
    - /srv/salt/prd

pillar_roots:
  base:
    - /srv/pillar
    - /srv/pillar/prd
  dev:
    - /srv/pillar/dev
    - /srv/pillar/pre
    - /srv/pillar/prd
  pre:
    - /srv/pillar/pre
    - /srv/pillar/prd
  prd:
    - /srv/pillar/prd
```

That's it!  From now on, to add a computer to an environment, simply add the computer's hostname the the respective list:
* [dev](https://github.com/VertigoRay/salt-grains-environment/blob/master/_grains/environment.py#L25-L29)
* [pre](https://github.com/VertigoRay/salt-grains-environment/blob/master/_grains/environment.py#L34-L36)

### top.sls

This is a sample of SaltStack's [top file](http://docs.saltstack.com/ref/states/top.html).  This will show you how to reference the grain created in this repo:

```yaml
base:
  '*':
    - global

prd:
  'environment:prd':
    - match: grain
    - apps.salt-minion

pre:
  'environment:pre':
    - match: grain
    - apps.dsconfigad

dev:
  'environment:dev':
    - match: grain
    - apps.testapp
```

## Contributing

### Issues

All features and bug reports must be tracked as GitHub Issues before any work begins. Issues are the authoritative record of intent — PRs without a corresponding issue will not be merged.

### Pull Requests

All changes require a PR. Direct commits to the default branch are not permitted.

**PR requirements:**
- Reference the issue in the PR description (`Closes #N` or `Ref #N`)
- Include a `CHANGELOG.md` entry — one entry per issue, referencing the issue number for full context
- All code review comments must be acknowledged and addressed before merge — no unresolved items at merge time
