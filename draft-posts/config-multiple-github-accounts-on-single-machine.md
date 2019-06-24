### Config multiple github accounts on single machine
Assume we have 2 github account with 2 email:
  - xxx@xxx.com
  - yyy@yyy.com

#### 1. Generate new ssh key
```
ssh-keygen -t rsa -C "xxx@xxx.com"
ssh-keygen -t rsa -C "yyy@yyy.com"
```

Remember set name for SSH KEY. Example:

```
~/.ssh/id_rsa_xxx
~/.ssh/id_rsa_xxx.pub
~/.ssh/id_rsa_yyy
~/.ssh/id_rsa_yyy.pub
```

#### 2. Add 2 Public SSH key (*.pub) to 2 github account [setting page](https://github.com/settings/keys).

#### 3. Edit SSH config

```
cd ~/.ssh/
vi config
```

Then added

```
#xxx account
Host xxx
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_xxx
  IdentitiesOnly yes 

#yyy account
Host yyy
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_yyy
  IdentitiesOnly yes
```
[IdentitiesOnly yes](https://serverfault.com/questions/450796/how-could-i-stop-ssh-offering-a-wrong-key/450807#450807) prevent brute force search SSH KEY 


#### 4. Config each project
- Folk project (Next step if you are owner project)
- Clone project, get link from "Clone with SSH"

```
git@github.com:xxx/PROJECT_NAME.git

replace <github.com> by any Host value(in ~/.ssh/config)

Example:
If you want add xxx account for PROJECT_NAME project
git clone git@xxx:xxx/PROJECT_NAME.git
```

- cd project to config name, email
```
$ git config user.name "xxx"
$ git config user.email "xxx@xxx.com" 
```

#### 5. Try to create pull request

