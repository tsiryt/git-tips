# git-tips
Tips to use Git.

## Git authentification

### Variables location

First, verify where is $HOME : `echo $HOME`, setting the variable if necessary.

### SSH authentication

#### Generate keys

```
ssh-keygen -t ed25519 -C "my.identity@domain.com"
```

By default, this will save our keys in ~/.ssh/id_ed25519 and ~/.ssh/id_ed25519.pub.

#### Load public key into Git

We copy the contents of our public key, using either one of the following :

- open ~/.ssh/id_ed25519 in a text editor and copy the contents
- `cat ~/.ssh/id_ed25519.pub | clip`

We can then paste the public key into Git.

#### Verify

To check if we can access Git, we can run the following :

```
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_ed25519
ssh -vvv <git adress>
```

### Authentication problems

Having done the above, it is possible that in a new session, we cannot login to Git. An error like this one, for example, would point to that :

```
<git adress> password :
```

We can repeat the steps described in **Verify**, if this allows us to login, it means that our credentials are correct, but we need to log them in every session, either manually or using the following procedure.

- navigate to $HOME
- if a .bash_profile file exists, add the following code to it. If not, create it : `nano .bash_profile`
- add this code :

```
SSH_ENV="$HOME/.ssh/agent-environment"
 
function start_agent {
    echo "Initialising new SSH agent..."
    /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
    echo succeeded
    chmod 600 "${SSH_ENV}"
    . "${SSH_ENV}" > /dev/null
    /usr/bin/ssh-add;
}
 
# Source SSH settings, if applicable
 
if [ -f "${SSH_ENV}" ]; then
    . "${SSH_ENV}" > /dev/null
    #ps ${SSH_AGENT_PID} doesn't work under cywgin
    ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
        start_agent;
    }
else
    start_agent;
fi
```

- now, we load this code : `source .bash_profile`

