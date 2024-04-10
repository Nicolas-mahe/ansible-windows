### Delegation

We will change the handlers created in lab05 and adapt it to run it locally instead of using the distant host.

1) Add `delegate_to: localhost` and custom vars under the handler as shown here

```yml
- name: "Send Teams notification"
  ansible.builtin.uri:
    url: "{{ teams_webhook_url }}"
    method: POST
    body_format: json
    body:
      type: AdaptiveCard
      text: "{{ ansible_date_time.date }}: index.html has been updated on {{ ansible_host }}"
  delegate_to: localhost
  vars:
    ansible_connection: local
    ansible_shell_type: /bin/sh
```

This changes the module and some parameters to adapt to a Linux environment

2) run again the playbook and see the result