# Configure Dev environment

## Requirements

- Dedicated Azure resource group
- WSL2
- Visual Studio Code + WSL extension

## Loop and conditionnal in ansible

1) Create a variable called users like the following
```yml
users:
  - name: mytempuser01
    password: mytemppassword01
  - name: mytempuser02
    password: mytemppassword02
  - name: mytempuser03
    password: mytemppassword03
```
2) Create all the users in the list with only one tasks, refer to this documentation : 
   https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#iterating-over-a-simple-list
   
3) Delete all the users in the list with only one tasks, put it before the creation task
   
4) Adapt the list and add group to each one
```yml
users:
  - name: mytempuser01
    password: mytemppassword01
    groups:
      - users
      - administrators
  - name: mytempuser02
    password: mytemppassword02
    groups:
      - users
      - administrators
  - name: mytempuser03
    password: mytemppassword03
    groups:
      - users
      - administrators
```
   
5) Create all the users and their associated group if only it is not created
   
6) Rename the loop variable to a custom one (named user for example) and refactor the necessary tasks

## Tags management

1) Add tags to all of your tasks and separate them : one one side a user_creation tag and on the other a user_deletion tag
   
2) Run the playbook but only select the user_deletion

3) Run the playbook but only select the user_creation