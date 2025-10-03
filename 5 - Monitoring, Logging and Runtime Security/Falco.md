# Falco

```shell
systemctl status falco

journalctl -fu falco

cat /var/run/falco.pid

kill -1 $(cat /var/run/falco.pid)

```

Regras são definidas em rules.yaml, contendo rules.

Nas rules, em conditions, posso usar como parâmetro:

- container.id é o nome do container;
- proc.name é o nome do processo;
- fd.name é o nome do descritor de arquivo;
- evt.type que é o nome da chamada do sistema, como execve, open, accept, connect etc.;
- user.name que é o nome do usuário que gerou a ação;
- container.image.repository é o nome da image.

Nas rules, em priorities, possuo usar:

- DEBUG;
- INFORMATIONAL;
- NOTICE;
- WARNING;
- ERROR;
- CRITICAL;
- ALERT;
- EMERGENCY.

A configuração do Falco fica em /etc/falco/falco.yaml


