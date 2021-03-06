# infra-upgrade

- Upgrade all of our debians nodes while taking care of holding local
  packages
- Commit changes to `/etc` before upgrading if needed
- Keep a list of upgraded packages with pre and post version in `/etc/upgraded/$DATE`
- Commit changes to `/etc` after upgrading if needed

See [CHANGELOG][]

[CHANGELOG]: CHANGELOG.md

# Basic setup for ansible and local private data

- Basic setup was made using [init-play-dir.yml](init-play-dir.yml)

```bash
gist=https://gist.githubusercontent.com
user=thydel

file=ansible.mk
gistid=c5ba9cb9e4d3fb18d8452ca5ad9217df
curl -s $gist/$user/$gistid/raw > $file; chmod +x $file;
```

- Helper file [ansible.mk](ansible.mk) come from

```bash
file=init-play-dir.yml
gistid=f3cbc54b05ed5d6dbecb7e6f4c86a6cf
curl -s $gist/$user/$gistid/raw > $file; chmod +x $file;
```

- Run `ansible.mk help` to get hints on `ansible` (and `pass`) setup

- Run `init-play-dir.yml -e use_ssh_config=True` to initialize `ansible` conf

- Choose the `data_version` to use in [infra-data.yml](infra-data.yml)

- Run `infra-data.yml` to populate `inventory`, `ssh-config` and `var/ext`

- Run `ansible-galaxy install -r requirements.yml` to get required *roles*

# Usage

## First, hold local packages

```bash
apt-hold.yml -l "$pattern" -C
apt-hold.yml -e notfoundok=True -l "$pattern" -C
apt-hold.yml -e notfoundok=True -e show=True -l "$pattern"
```

- The default search expression for packages to put on hold is
  `?and(?maintainer(epiconcept), ?installed)`
- When option `notfoundok` is `True` it is not an error for the search
  result list to be empty
- Option `show` gives a list of currently held packages, a list of
  found packages matching search expression and the difference between
  held and found

## Then upgrade

```bash
apt-upgrade.yml -l "$pattern" -e show=True -DC
apt-upgrade.yml -l "$pattern" -e show=True -e com=True -D
```
