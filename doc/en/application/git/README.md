## Installation

* `# pacman -S git` Install
* `# git config --global user.name "aredots"` Set username
* `# git config --global user.email info@aredots.com` Set email 
* `# git config --global core.editor emacs` Set default editor
* `# git remote add origin ssh://aredots/guides` Add remote repository SSH
* `# git remote add origin http://aredots/guides` Add remote repository HTTPS

## Services
### GitLab
#### Dependencies

| Name    | Type    | Source | Guide |
| ------- | ------- | ------ | ----- |
| openssh | package | [Arch Linux](https://security.archlinux.org/package/openssh)| - |
| git     | package | [Arch Linux](https://security.archlinux.org/package/git)| [aredots](https://gitlab.com/aredots/guides/blob/master/doc/en/application/git.md) |
| SSH Encryption| knowledge | - | [External](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)

#### Configuration
* `# ssh-keygen -t ed25519 -C "info@aredots.com"` Generate a new ECD25519 SSH key pair
	+ `default` Default path
	+ `default` No passphrase
	+ `default` Confirm passphrase
* `# xclip -sel clip < ~/.ssh/id_ed25519.pub` Copy the public SSH key to the clipboard
* Login into the GitLab account [here](https://gitlab.com/users/sign_in)
* Add the public SSH key to the GitLab account [here](https://gitlab.com/profile/keys)
* `# ssh -T git@gitlab.com` Test
	+ `yes` Answer *yes*

#### Tips and tricks
* `# git config core.sshCommand "ssh -o IdentitiesOnly=Yyes -i ~/.ssh/<private-key-filename-for-the-repository> -F /dev/null"` If you want to use different keys depending on the repository you are working on

More infos on the official [GitLab documentation](https://gitlab.com/help/ssh/README) page. 
