---
layout: post
title:  "Ansible Tips and Tricks"
tags: ansible
categories: ansible
---

# Intro

I did ansible 2 years ago (apparently I honestly don't remember doing it but I was told I output a bunch of playbooks) and surprise sunrise I forgot everything. So Hopefully I can use this page to capture some little tricks so I can remember how it works.

## Debugging

Something going wrong? Use the `debug` module. Pretty handy

```yaml
---
- name: Debug Stuff.
  gather_facts: false
  hosts: 'localhost'
  - name: LS
    shell: "ls ~/"
    register: ls
  - debug: var=ls.stdout_lines
```
