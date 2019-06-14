# Multiple SSH keys for different accounts on Github or Gitlab?

1. $ ssh-keygen -t rsa -C "your_name@home_email.com"
2. input a new path
3. config ~/.ssh/config 

# Home account
Host home.github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_home

# Company account
Host company.github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_company


# how to checkout a remote git branch

1. git fetch # to fetch the new branch
2. git checkout -b [name] origin/[remote branch name]

