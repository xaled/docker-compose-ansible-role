# Template Ansible Role for Docker Compose Projects

This is a template repository for an Ansible role that can be used to deploy Docker Compose projects.
This Ansible role facilitates the installation and updating of a docker-compose project on remote servers, as well as enabling log retrieval from the server.

Here is the structure of this repository:
```
├── roles
│   └── projectx
│       ├── files
│       │   └── docker-compose.yml
│       ├── templates
│       │   └── .env.j2
│       ├── defaults
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       ├── handlers
│       │   └── main.yml
│       └── README.md
├── playbooks
│   └── projectx_playbook_sample.yml
├── LICENSE
└── README.md
```

## Usage

1. Clone the repository and rename the `roles/projectx` to match your project name.
2. Create your Docker Compose project under the `files` directory of your role.
3. Customize the `.env.j2` file for your Docker Compose project.
4. Customize the `defaults/main.yml` variables to fit your project needs and include any new variables used in `.env.j2`.


## Playbook Usage
The `playbooks/projectx_playbook_sample.yml` file contains a sample playbook that can be used as a reference.

Here are examples of running this playbook with specific tags:
- To install the project, use the `install` tag:
```
ansible-playbook playbooks/projectx_playbook_sample.yml --tags install
```

- To update the installation, use the `update` or `update-config` tags:
    - To update everything except re-extracting compressed files: `ansible-playbook playbooks/projectx_playbook_sample.yml --tags update`
    - To skip updating packages: `ansible-playbook playbooks/projectx_playbook_sample.yml --tags update --skip-tags update-packages`
    - To update only the project directory: `ansible-playbook playbooks/projectx_playbook_sample.yml --tags update-config`
    - To update the project directory and rerun first-run tasks, including re-extracting compressed files: `ansible-playbook playbooks/projectx_playbook_sample.yml --tags update-config,first-run`

- To pull logs from the host, use the `pull-logs` tag:
```
ansible-playbook playbooks/projectx_playbook_sample.yml --tags pull-logs
```

## Variables
Here are the variables used in this Ansible role:

- `project_name`: Sets the name of the project.
- `project_directory`: Sets the path of installation for the project. Default value is ``"/opt/{{ project_name }}"``.
- `src_project_directory`: Sets the path of the source project directory. Default value is `"{{ role_path }}/files/"`.
- `compose_files`: A list containing the path(s) of the docker-compose file(s). Default value is `["{{ project_directory }}/docker-compose.yml"]`.
- `sync`: Set options for synchronizing the project directory files:
  - `rsync_opts`: A list of rsync options to be used during file synchronization. Default value is `["--filter=:- .gitignore", "--exclude=.git", "--exclude=logs/", "--exclude=data/", "--exclude=data.tar.gz"]`.
  - `delete`: A boolean value indicating whether to delete files on the remote server that don't exist locally. Default value is "no".
  - `copy_links`: A boolean value indicating whether to copy symbolic links as links or as files. Default value is "yes".
- `compressed_files`: A list containing the source and destination paths for compressed files to be decompressed and transferred:
  - `src`: Sets the source path of the compressed file.
  - `dest`: Sets the destination path of the compressed file..
  - `remote_src`: A boolean value indicating whether the compressed file is located on the remote or local host.
- `logging`: Sets the options for logging:
  - `remote_directory`: Sets the remote directory where log files will be stored. Default value is `"{{ project_directory }}/logs"`.
  - `local_directory`: Sets the local directory where log files will be stored. Default value is `"{{ inventory_dir }}/logs"`.
  - `user`, `group` and `mode`: Set options for creating logs subdirectories.
  - `sources`: A list containing the options for logging sources.
- `compose_project_name`: Sets the the docker-compose [project name](https://docs.docker.com/compose/reference/). Default value is `"{{ project_name }}"`.


## License

This template repository is licensed under the MIT License. See the LICENSE file for details.