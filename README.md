# Drupal Dockerizer

## Requirements

- Python >= 3.5
- Docker
- Docker Compose
- Ansible >= 2.8
- Git

## Quickstart

### Prepare project structure

- `db`: crate this directory and download database dump here if exists.
- `code`: clone your project files into this directory.
- `drupal-dockerizer`: clone drupal-dockerizer project in this directory.
- `files`: create the directory and pull Drupal assets here

### Prepare your config for Drupal Dockerizer

```bash
cd drupal-dockerizer
cp default.config.yml config.yml
```

### Start new local environment

```bash
cd drupal-dockerizer
sudo ansible-playbook -vvv main.yml --connection=local --extra-vars=init_project=true
```

### Start local environment for existing project

```bash
cd drupal-dockerizer
sudo ansible-playbook -vvv main.yml --connection=local
```

### Import database from dump

```bash
cd drupal-dockerizer
sudo ansible-playbook -vvv db.yml --connection=local
```

### Xdebug setup

For advanced network setup set in `config.yml` variable `xdebug_enviroment` like this

```bash
remote_enable=1 remote_connect_back=1 remote_port=9008 remote_host=192.168.{network_id}.1 show_error_trace=0 show_local_vars=1 remote_autostart=1 show_exception_trace=0 idekey=VSCODE
```

For work Xdebug with advanced networking in vscode add to your launcher.json file in project next lines:

```json
    {
      "name": "XDebug Docker",
      "type": "php",
      "request": "launch",
      "hostname" : "192.168.{network_id}.1",
      "port": 9008,
      "pathMappings": {
        "/var/www": "${workspaceRoot}/path_to_drupal_project"
      },
      "xdebugSettings": {
        "show_hidden": 1,
        "max_data": -1,
        "max_depth": 2,
        "max_children": 100,
      }
    },
```

For MacOs or Windows machine set to:

```bash
remote_enable=1 remote_connect_back=0 remote_port=9000 remote_host=host.docker.internal show_error_trace=0 show_local_vars=1 remote_autostart=1 show_exception_trace=0 idekey=VSCODE
```

### Advanced Networking

You can use an advanced docker network to be able to conveniently host multiple Drupal projects on one computer.

Set in config.yml `advanced_networking: true`.

You can change the ip address of the project using the variable `network_id` in `config.yml` file

You can change doamin name by variable `domain_name` in `config.yml` file

#### Advanced Networking Limitation

The advanced network only works on a machine with a Linux distribution.

#### Advanced Networking project structure

- `Adminer` placed on domain name and on 8080 port.
- Solr 4 placed on domain name and on 8983 port and /solr path. `http://drupal.devel:8983/solr` for examle.
- Data Base placed on 192.168.<<network_id>>.13 and on 3306 port. You can connect to DB by vscode [extension](https://marketplace.visualstudio.com/items?itemName=formulahendry.vscode-mysql) or from `Adminer`

### Drush usage

Run in terminal: `docker exec my-project-php74 drush <drush-command>` for run drush command.
If you change compose project name or php version in config replace `my-project-php74` to `<compose_project_name>-<phpversion>`.

Use the next command in order to ran multiple Drush commands.

```bash
sudo ansible-playbook -vvv run-drush-commands.yml --connection=local
````

Change `drush_commands` variable in your `config.yml` file like this:

```yaml
drush_commands:
  - 'updb'
  - '-v sapi-r'
  - '-v sapi-i'
  - ...
```

## FAQ

### How to reset everything and start from scratch?

```bash
cd <project>/docker
sudo ansible-playbook -vvv reset.yml --connection=local
```
