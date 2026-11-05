#!/usr/bin/env sh

# Run by the player, from inside the game, as "./setup_ssh".
# Creates a dedicated ssh key for github and the matching ~/.ssh/config entry.
#
# Inside the game $HOME is the game's World directory, so "~/.ssh" is the
# game's own .ssh.  The player chooses whether that should be a real directory
# (the key stays inside the game) or a symbolic link to their real ~/.ssh (the
# key also works outside the game).

printf '%s' "Do you want to setup the ssh authentification method to work outside of the game (y/n)? "
read -r globalinstall

if [ "$globalinstall" = "y" ]
then
    if [ ! -d "${REAL_HOME}/.ssh" ]
    then
        echo "creating ~/.ssh directory (outside the game)"
        mkdir "${REAL_HOME}/.ssh"
        chmod 700 "${REAL_HOME}/.ssh"
    fi
    # replace the game's .ssh (real directory or stale symlink) by a link to
    # the real one
    if [ -L "${GSH_HOME}/.ssh" ]
    then
        rm -f "${GSH_HOME}/.ssh"
    elif [ -e "${GSH_HOME}/.ssh" ]
    then
        rm -rf "${GSH_HOME}/.ssh"
    fi
    ln -s "${REAL_HOME}/.ssh" "${GSH_HOME}/.ssh"
    identityfile_path="${REAL_HOME}/.ssh/id_rsa.git"
else
    # the key stays inside the game: .ssh has to be a real directory
    if [ -L "${GSH_HOME}/.ssh" ]
    then
        rm -f "${GSH_HOME}/.ssh"
    elif [ -e "${GSH_HOME}/.ssh" ]
    then
        rm -rf "${GSH_HOME}/.ssh"
    fi
    mkdir "${GSH_HOME}/.ssh"
    chmod 700 "${GSH_HOME}/.ssh"
    identityfile_path="${GSH_HOME}/.ssh/id_rsa.git"
fi

# create ~/.ssh/id_rsa.git
if [ ! -e ~/.ssh/id_rsa.git ]
then
    ssh-keygen -t rsa -f ~/.ssh/id_rsa.git -N ""
    chmod 600 ~/.ssh/id_rsa.git
fi

# "IdentitiesOnly yes" matters: without it ssh still offers every key held by
# ssh-agent, and github accepts one of those instead of the key we just made.
# A player who already works with github on this machine would then silently
# keep using their other account -- the mission would pass without the new key
# ever being tried, and the pushes of the next missions would go elsewhere.
github_config() {
    echo "Host github.com"
    echo "    Hostname github.com"
    echo "    User git"
    echo "    IdentityFile ${identityfile_path}"
    echo "    IdentitiesOnly yes"
}

if [ ! -e ~/.ssh/config ]
then
    github_config >> ~/.ssh/config
elif grep -q github.com ~/.ssh/config
then
    echo "file ~/.ssh/config already has instruction for github: The script did not try to edit it."
    echo "We do expect the following lines: (please check)"
    github_config
else
    echo "adding configuration for github in ssh config file"
    {
        echo ""
        github_config
    } >> ~/.ssh/config
fi

echo ""
echo "Step 1 completed. Now you can do Step 2:"
echo ""
echo "go to  https://github.com/settings/keys"
echo 'click on "New ssh key", put as title the name of this machine'
echo "and paste the following content:"
echo ""
echo ""
cat ~/.ssh/id_rsa.git.pub
