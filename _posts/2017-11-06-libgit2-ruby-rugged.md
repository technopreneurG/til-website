---
layout: post
title:  "Rugged : libgit2 bindings in Ruby"
tags: [git, libgit2, ruby, rugged]
date: 2017-11-06 01:53:35 +0530
created_at: 2017-11-06 01:53:35 +0530
updated_at: 2017-12-29 05:24:06 +0530
categories: til
---
# About Rugged
From [rugged's](https://github.com/libgit2/rugged) github page.

> Rugged is a library for accessing libgit2 in Ruby. It gives you the speed and portability of libgit2 with the beauty of the Ruby language.

# About libgit2
From [libgit2's](https://github.com/libgit2/libgit2) github page:

> libgit2 is a portable, pure C implementation of the Git core methods provided as a re-entrant linkable library with a solid API, allowing you to write native speed custom Git applications in any language which supports C bindings. 


>libgit2 provides you with the ability to manage Git repositories in the programming language of your choice. It's used in production to power many applications including GitHub.com, Plastic SCM and Visual Studio Team Services.

# Prep dev
```bash
#install ruby
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
$ curl -sSL https://get.rvm.io | bash -s stable --ruby
$ usermod -aG rvm vagrant
$ source /etc/profile.d/rvm.sh

#install necessary packages
$ apt-get update
$ apt-get install -y build-essential git libssh2-1-dev cmake

#install gems
$ gem install bundler -N
$ gem install rugged -v 0.26.0

```

# SSH Key Setup
```bash

ubuntu@ubuntu-xenial:/demo$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): (I tried with blank passphrase)
Enter same passphrase again: 
Your identification has been saved in /home/ubuntu/.ssh/id_rsa.
Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:cGzeKom05nEaj2egPzWoInXY0UTFw5bBl5ohBpJJntI ubuntu@ubuntu-xenial
The key's randomart image is:
+---[RSA 2048]----+
| .oo.o.=oo .     |
| ooo  +.O o      |
|. E  +.o+*       |
| .  . .=o.       |
|   o.o  S .      |
|  o.=oo. .       |
| . +*o+..        |
|o oo.Bo.         |
|.. .=+.          |
+----[SHA256]-----+

ubuntu@ubuntu-xenial:/demo$ cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDgBtsWT2p52JUFHMZF1XpXSDp4rymxSrgi2agUK52/mQdBR1IUSshM3GYSYhXOspYpTfxXzvheqKQXVkGoMUVxX67K+pEKbCbV1uwZ+kLsM9zGSTrGqFF/5vxNbCH+miDQt23MQUABVccTS0RpaIRMZ49s/KPuoLgDW7VsG8BDGy43p21syB8SOIiMULOZplkc1FMofy64iVwH/NrRVQsACecz6r+6xHVHDHT6u8UHUn9Zjkm9PJSRp6u1KCNNVdsPgjE4KxT7+GOJn+O5uAUSJvTHmYdDzvtsLAfRa/D98vVNt9KgetHekm37c4UsaEiHvj45TbN5RmZaYd66AUdz ubuntu@ubuntu-xenial

```

# Set access key(Bitbucket)
- Login to <https://bitbucket.org/>
- Go to Account settings page
- Click SSH Keys and Add key
- Enter label for your key
- Copy the content of id_rsa.pub above and paste into the Key field
- Click save
- Go back to your terminal window and try
```bash
$ ssh -T git@bitbucket.org
logged in as username.
You can use git or hg to connect to Bitbucket. Shell access is disabled.
#if your keys are in different location:
$ ssh -i /path/to/rsa-file -T git@bitbucket.org
```

# Set access key(github)
- Login to <https://github.com/>
- Go to the settings page
- Click on SSH and GPG keys
- Click on New SSH Key
- Enter Title, any descriptive name for your key
- Copy the content of id_rsa.pub and paste into the Key field
- Click on Add SSH key
- Go back to your terminal window and try:
```bash
$ ssh -T git@github.com
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
# if your keys are in different location:
$ ssh -i /path/to/rsa-file -T git@github.com
```


# Rugged: clone existing repository
```ruby
require 'rugged'

ssh_key_credential = Rugged::Credentials::SshKey.new({
        username:   ENV["GIT_REMOTE_SSH_USER"],
        publickey:  ENV["GIT_REMOTE_SSH_PUBKEY"],
        privatekey: ENV["GIT_REMOTE_SSH_KEY"],
        passphrase: ENV["GIT_REMOTE_SSH_PASSPHASE"],
})
 
dir = "/tmp/test1"
#$ git clone $GIT_REMOTE_SSH_URL
repo = Rugged::Repository.clone_at(ENV['GIT_REMOTE_SSH_URL'], dir, {
  credentials: ssh_key_credential
})
```
- [Rugged::Credentials::SshKey](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Credentials/SshKey) - A ssh key credential object that can optionally be passphrase-protected
- [clone_at](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Repository#clone_at-class_method) - Clone a repository from url to local_path.


# Rugged: add new file, commit and push to remote repo
```ruby
filename = dir + '/test1.txt'
author = {:name => 'Author Name', :email => 'author-name@gmail.com'}
File.write(filename, 'hello world')

#$ git add $filename
repo.index.add(filename)
repo.index.write

commit = Rugged::Commit.create(repo,
      	  :author => author,
      	  :message => "Commit Message",
      	  :committer => author,
      	  :parents => repo.empty? ? [] : [ repo.head.target ].compact,
      	  :tree => repo.index.write_tree(repo),
      	  :update_ref => "HEAD")

#$ git push
repo.remotes['origin'].push(['refs/heads/master'], {
  credentials: ssh_key_credential
})
```
- [index.add](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Index:add) - Add a new entry to the index or update an existing entry in the index.
- [index.write](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Index:write) - Writes the index object from memory back to the disk, persisting all changes.
- [Commit.create](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Commit.create) - Write a new Commit object to repository.
- [Remote.push](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Remote:push) - Pushes the given refspecs to the given remote.

# Rugged: pull latest changes from remote repo(fetch + merge)
```ruby
#$ git pull

#$ git remote -v
#$ git fetch
repo.remotes['origin'].fetch(['refs/heads/master'], {
  credentials: ssh_key_credential
})

#$ git branch -avv
remote_branch = repo.branches['origin/master']
analysis = repo.merge_analysis(remote_branch)
if analysis.include?(:fastforward)
#$ git merge
  repo.checkout_tree(remote_branch.target)
  repo.references.update(repo.head.resolve, remote_branch.target_id)
  repo.index.write
  repo.checkout_head({ strategy: :force })
end
```
- [Remote.fetch](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Remote:fetch) - Downloads new data from the remote for the given refspecs and updates tips.
- [merge_analysis](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Repository:merge_analysis) - Analyzes the given commit and determines the opportunities for merging it into the repository's HEAD.
- [checkout_tree](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Repository:checkout_tree) - Updates files in the index and working tree to match the content of the tree pointed at by the treeish.
- [ReferenceCollection#update](http://www.rubydoc.info/github/libgit2/rugged/Rugged/ReferenceCollection:update) - Set the target of a reference.
- [checkout_head](http://www.rubydoc.info/github/libgit2/rugged/Rugged/Repository:checkout_head) - Updates files in the index and the working tree to match the content of the commit pointed at by HEAD.

# putting it all together
```ruby
#file: test.rb
require 'rugged'

def git_clone(repo_url, dir, ssh_key_credential)
    if !Dir.exist?(dir)
        repo = Rugged::Repository.clone_at(repo_url, dir, {
            credentials: ssh_key_credential
        })
    else
        repo = Rugged::Repository.new(dir)
    end     
        
    repo
end

def git_pull(repo, ssh_key_credential)
    repo.remotes['origin'].fetch(['refs/heads/master'], {
        credentials: ssh_key_credential
    }) 
        
    other_branch = repo.branches['origin/master']
        
    analysis = repo.merge_analysis(other_branch.target)
    if !analysis.include?(:up_to_date) && analysis.include?(:fastforward)
        repo.checkout_tree(other_branch.target)
        repo.references.update(repo.head.resolve, other_branch.target_id)
        repo.index.write
        repo.checkout_head({ strategy: :force })
    end
end

def git_commit(repo, filename)
    repo.config['push.default']='simple'

    repo.index.add(filename)
    repo.index.write

    author = {:name => 'Test', :email => 'test@gmail.com'}
    commit = Rugged::Commit.create(repo,
                                   :author => author,
                                   :message => "Hello",
                                   :committer => author,
                                   :parents => repo.empty? ? [] : [ repo.head.target ].compact,
                                   :tree => repo.index.write_tree(repo),
                                   :update_ref => "HEAD")

end

def git_push(repo, ssh_key_credential)
    repo.remotes['origin'].push(['refs/heads/master'], {
        credentials: ssh_key_credential
    })
end

begin
    ssh_key_credential = Rugged::Credentials::SshKey.new({
        username:   ENV["GIT_REMOTE_SSH_USER"],
        publickey:  ENV["GIT_REMOTE_SSH_PUBKEY"],
        privatekey: ENV["GIT_REMOTE_SSH_KEY"],
        passphrase: ENV["GIT_REMOTE_SSH_PASSPHASE"],
    })

    dir = "/tmp/checkout/test1"
    repo = git_clone ENV['GIT_REMOTE_SSH_URL'], dir, ssh_key_credential

    filename = 'test1.txt'
    filepath = dir+'/'+filename

    if !File.exist?(filepath)
        File.write(filepath, 'hello world')

        git_commit(repo, filename)

        git_push(repo, ssh_key_credential)
    end

    git_pull(repo, ssh_key_credential)
rescue Exception => e
    puts e.message
    puts e.backtrace.inspect
end

```
run the script:
```bash
#bitbucket
$ GIT_REMOTE_SSH_USER="git" GIT_REMOTE_SSH_PUBKEY="/home/ubuntu/.ssh/id_rsa.pub" \ 
    GIT_REMOTE_SSH_KEY="/home/ubuntu/.ssh/id_rsa" GIT_REMOTE_SSH_PASSPHASE="***" \
    GIT_REMOTE_SSH_URL="git@bitbucket.org:username/test.git" ruby test.rb

#github
$ GIT_REMOTE_SSH_USER="git" GIT_REMOTE_SSH_PUBKEY="/home/ubuntu/.ssh/id_rsa.pub" \
    GIT_REMOTE_SSH_KEY="/home/ubuntu/.ssh/id_rsa" GIT_REMOTE_SSH_PASSPHASE="" \
    GIT_REMOTE_SSH_URL="git@github.com:username/test.git" ruby test.rb
```

References:
- <https://github.com/libgit2/rugged>
- <http://www.rubydoc.info/github/libgit2/rugged/>
- <https://github.com/libgit2/rugged/tree/master/test>
