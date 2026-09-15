# Traefik v3 + Authelia (Ansible playbook)

Разворачивает Traefik v3 в связке с Authelia (2FA / forward-auth) через File Provider
на Docker-хосте, с проксированием Grafana, Prometheus и Proxmox.

## Структура

```
.
├── playbook.yml
├── inventory.example.ini
├── group_vars/all/
│   ├── vars.yml            # открытые переменные (домен, IP, список пользователей)
│   └── vault.yml.example   # шаблон секретов — скопировать в vault.yml и зашифровать
└── templates/               # Jinja2-шаблоны конфигов Traefik/Authelia
```

## Быстрый старт

1. Скопируйте inventory и пропишите свой хост:
   ```bash
   cp inventory.example.ini inventory.ini
   ```

2. Отредактируйте `group_vars/all/vars.yml` — домен, IP бэкендов, список пользователей.

3. Создайте секреты:
   ```bash
   cp group_vars/all/vault.yml.example group_vars/all/vault.yml
   ```
   Заполните значения (для случайных секретов):
   ```bash
   openssl rand -hex 32
   ```
   Хэш пароля пользователя Authelia:
   ```bash
   docker run --rm authelia/authelia:latest authelia crypto hash generate argon2 --password 'ВАШ_ПАРОЛЬ'
   ```
   Зашифруйте файл:
   ```bash
   ansible-vault encrypt group_vars/all/vault.yml
   ```

4. Запустите playbook:
   ```bash
   ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass
   ```
