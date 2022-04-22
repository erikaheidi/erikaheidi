---
title: Comic: Agent vs Agentless, and The Anarquist Tool
published: true
description: In this comic, we'll discuss why configuration management tools are often complex in nature, and why Ansible is a viable alternative to automate your infrastructure setup.
tags: devops, beginners, ansible, comics
series: Configuration Management 101
cover_image: https://dev-to-uploads.s3.amazonaws.com/i/8hnbcxj9ie9mnc6ewtzh.jpg
---

In this comic, we'll discuss why configuration management tools are often complex in nature, and why Ansible is a viable alternative to automate your infrastructure setup.

![comic - agent vs agentless](https://dev-to-uploads.s3.amazonaws.com/i/vn405vh2qhd06uxgzntj.jpg)

Transcript:

> Configuration Management Tools can be complex, and some will require more setup work than others. That is because these tools deal with **system states**. They're not "just scripts".
Most configuration management tools require an agent installed on the remote server you want to control. The agent will provide information about that system and execute instructions from a controlling server. While this creates a tight control over the nodes, it can greatly complicate the initial setup of your fleet, also when adding a new server.
There's one tool, however, which does things differently. It doesn't require specialized software to be installed on nodes. It uses **SSH** to communicate and manage hosts from a controller machine. This tool is called **Ansible**. Ansible is free and open source.

[Ansible](https://ansible.com) is a configuration management that doesn't require any specialized software to be installed on the machines you want to control. For that reason, and because it uses YAML for defining provisioning script, it is a great tool to get started with configuration management, and it can be used to manage from small to very large infrastructure environments.

Some of the most important characteristics of Ansible are:

- Python-based
- Agentless nature, uses SSH
- Idempotent behavior
- Uses YAML files to define automation
- Sequential execution of tasks
- Batteries-included: hundreds of built-in modules
- Templating system (Jinja2)
- Can be extended in any programming language able to return JSON

### Learn More about Ansible

In this webinar, I talk about Ansible and present a live demo deploying a LEMP + Laravel application to remote servers.

{% youtube EVsqdS8kWSQ %}

Ansible Tutorials:

- [How to Install and Configure Ansible on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-18-04)
- [Configuration Management 101: Writing Ansible Playbooks](https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks)
- [How to Use Ansible: A Reference Guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)
