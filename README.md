# ansible-role-monit

[![Build Status](https://travis-ci.org/linuxhq/ansible-role-monit.svg?branch=master)](https://travis-ci.org/linuxhq/ansible-role-monit)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-monit-blue.svg?style=flat)](https://galaxy.ansible.com/linuxhq/monit)
[![License](https://img.shields.io/badge/license-GPLv3-brightgreen.svg?style=flat)](COPYING)

RHEL/CentOS - Easy, proactive monitoring of processes, programs, files, directories, filesystems and hosts

This role currently only supports filesystem and program checks

## Requirements

None

## Role Variables

Available variables are listed below, along with default values:

    monit_alert:
     - recipient: root
    monit_daemon: 30
    monit_filesystem: []
    monit_httpd:
      - port 2812
      - use address localhost
      - allow localhost
      - allow admin:monit
    monit_httpd_ssl: {}
    monit_include: /etc/monit.d/*
    monit_log: syslog
    monit_mailformat:
      from: Monit <monit@$HOST>
      subject: monit alert --  $EVENT $SERVICE
      message: |
        $EVENT Service $SERVICE
             Date:        $DATE
             Action:      $ACTION
             Host:        $HOST
             Description: $DESCRIPTION

        Your faithful employee,
        Monit
    monit_mailserver: localhost
    monit_program: []
    monit_start_delay: 0

## Dependencies

None

## Example Playbook

    - hosts: servers
      roles:
        - role: linuxhq.monit
          monit_alert:
            - recipient: monit@pagerduty.com
              condition: not on
              events:
                - instance
                - action
          monit_daemon: 60
          monit_filesystem:
            - name: linuxhq_root
              path: /dev/mapper/vg-root
              actions:
                - if space usage gt 80%
                - for 5 cycles then alert
          monit_httpd:
            - unixsocket /var/run/monit.sock
            - allow admin:monit
          monit_program:
            - name: dnscrypt-proxy
              path: /usr/bin/systemctl --quiet is-active dnscrypt-proxy.service
              actions:
                - if status ne 0
                - for 5 cycles then alert
            - name: httpd
              path: /usr/bin/systemctl --quiet is-active httpd.service
              actions:
                - if status ne 0
                - for 5 cycles then alert
          monit_start_delay: 120

## License

Copyright (C) 2021 Taylor Kimball <tkimball@linuxhq.org>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.
