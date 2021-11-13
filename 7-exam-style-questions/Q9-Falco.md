### Question - Falco

### K8s Docs

[Falco](https://falco.org/docs/)

A pod in the "space" namespace has produced an alert that a shell was opened in the container of this pod.

Find the falco rule that produces this alert and change the output to include the "user id", "container id", "container image repo"

#### 1 - Setup falco rule

```sh

- Go to /etc/falco/falco_rules.yaml
- Then search for "shell in a container"

Copy the rule from there to /etc/falco/falco_rules.local.yaml

- rule: Terminal shell in container
  desc: A shell was used as the entrypoint/exec point into a container with an attached terminal.
  condition: >
    spawned_process and container
    and shell_procs and proc.tty != 0
    and container_entrypoint
    and not user_expected_terminal_shell_in_container_conditions
  output: >
    %evt.time.s,%user.uid,%container.id,%container.image.repository
  priority: ALERT
  tags: [container, shell, mitre_execution]

Once you've applied it, apply:

systemctl restart falco.service to over ride the current rule

```
