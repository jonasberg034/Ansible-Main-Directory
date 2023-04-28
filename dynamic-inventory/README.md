- Attach admin role to control-node

- Make a directory named ```dynamic-inventory``` under the home directory and cd into it.

```bash 
mkdir dynamic-inventory
cd dynamic-inventory
```

- Create a file named ```inventory.txt``` with the command below.

```bash
nano inventory.txt
```

- Paste the content below into the inventory.txt file.

- Along with the hands-on, public or private IPs can be used.

```txt
[servers]
db_server   ansible_host=<YOUR-DB-SERVER-IP>   ansible_user=ec2-user  ansible_ssh_private_key_file=~/<YOUR-PEM-FILE>
web_server  ansible_host=<YOUR-WEB-SERVER-IP>  ansible_user=ec2-user  ansible_ssh_private_key_file=~/<YOUR-PEM-FILE>

- Create file named ```ansible.cfg``` under the the ```dynamic-inventory``` directory.

```bash
nano ansible.cfg
```

```cfg
[defaults]
host_key_checking = False
inventory=/home/ec2-user/dynamic-inventory/inventory.txt
interpreter_python=auto_silent
private_key_file=~/<pem file>
```

- Create a file named ```ping-playbook.yml``` and paste the content below.

```bash
nano ping-playbook.yml
```

```yml
- name: ping them all
  hosts: all
  tasks:
    - name: pinging
      ansible.builtin.ping:
```

- Run the command below for pinging the servers.

```bash
ansible-playbook ping-playbook.yml
```

### Working with dynamic inventory

- go to AWS Management Consol and select the IAM roles:

- click the  "create role" then create a role with "AmazonEC2FullAccess"

- go to EC2 instance Dashboard, and select the control-node instance

- select actions -> security -> modify IAM role

- select the role thay you have jsut created for EC2 full access and save it.

- install "boto3 and botocore"

```bash
# sudo yum install pip
pip install --user boto3 botocore
```

- Create another file named ```inventory_aws_ec2.yml``` in the project directory.

```bash
nano inventory_aws_ec2.yml
```

```yml
plugin: amazon.aws.aws_ec2
regions:
 - us-east-1
filters:
  instance-state-name: running
#   tag:environment: dev
keyed_groups:
  - prefix: merhaba
    separator: "lar"
    key: tags.environment
compose:
  ansible_host: public_ip_address
  foo: private_ip_address
  boo: instance_id
  key: key_name
```
- see the inventory

```bash
ansible-inventory -i inventory_aws_ec2.yml --graph