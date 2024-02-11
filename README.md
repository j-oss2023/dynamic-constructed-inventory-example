repo to document how you can use a `-e, --extra_var` to filter on any hostvars from a `dynamic`, `constructed` inventory using `ansible.builtin.constructed` as well as a custom dynamic inventory plugin.


**./dynamic-mysql.yml:**
```yaml
#   MySQL Dynamic Inventory
#   https://github.com/j-oss2023/mysql-dynamic-inventory # Custom Dynamic Plugin
#   
#   Required columns: 
#   'inventory_group', 'inventory_hostname'
#
#   TLDR:
#   Besides the required columns, each column will be assigned 
#   as an ansible hostvar dynamically.

plugin: mysql-dynamic-inventory
db_host: backend
db_user: ansible_db_user
db_pass: ansible_db_pass
db_name: customers
db_query: |
  SELECT 
    'motorvehicle_co' as inventory_group,
    host as inventory_hostname,
    address as ansible_host,
    account as customer from dealerships
```

**./constructed.yml**
```yaml
---
plugin: ansible.builtin.constructed
strict: true
use_extra_vars: true
compose:
  play_customer: play_customer
groups:
  target: customer == play_customer
```


`ansible-playbook -i ./dynamic-mysql.yml -i ./constructed.yml playbooks/setup.yml -e "play_customer='Ferrari'" -l target_customer`
