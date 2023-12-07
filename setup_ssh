read -p "Do you want to setup the ssh authentification method to work outside of the game (y/n)?" globalinstall


cd ${GSH_HOME}
# first setup ~/.ssh as a symlink or as a real directory depending of the above choice.
if [[ "$globalinstall" == "y" ]];
then
    if [ ! -d ${REAL_HOME}/.ssh ]
    then
	echo "creating ~/.ssh directory (outside the game)"
	mkdir ${REAL_HOME}/.ssh
	chmod 700 ${GSH_HOME}/.ssh
    fi
    # remove local directory if .ssh is not a symlink
    # or create (or reset) the symlink
    if [ ! -L ${GSH_HOME}/.ssh ] && [ -e ${GSH_HOME}/.ssh ]
    then
	rm -rf ${GSH_HOME}/.ssh &> /dev/null
    elif [ -L ${GSH_HOME}/.ssh ]
    then
	unlink  ${GSH_HOME}/.ssh &> /dev/null
	ln -s ${REAL_HOME}/.ssh
    else
	ln -s ${REAL_HOME}/.ssh
    fi   
else
    # need to have it's own directory:
    if [ ! -L ${GSH_HOME}/.ssh ] && [ -e ${GSH_HOME}/.ssh ] # if a symlink -> unlink + create directory
    then
      unlink  ${GSH_HOME}/.ssh &> /dev/null
    elif [ -e ${GSH_HOME}/.ssh ] # hard directory already exists -> clean it
    then
	rm -rf ${GSH_HOME}/.ssh	
    fi
    
    mkdir ${GSH_HOME}/.ssh
    chmod 700 ${GSH_HOME}/.ssh
fi

#Now .ssh is setup either as a symlink (if user ask for global installation) or as an empty real directory.

# create ~/.ssh/id_rsa.git
if [ ! -e ~/.ssh/id_rsa.git ]
then
    ssh-keygen -t rsa -f ~/.ssh/id_rsa.git -N ""
    chmod 600  ~/.ssh/id_rsa.git
fi

if [[ "$globalinstall" == "y" ]];
then
    identityfile_path="${REAL_HOME}/.ssh/id_rsa.git"
else
    identityfile_path="${GSH_HOME}/.ssh/id_rsa.git"
fi

if [ ! -e  ~/.ssh/config ]
then
    echo "Host github.com" >> ~/.ssh/config
    echo "    Hostname github.com"  >> ~/.ssh/config
    echo "    User git" >> ~/.ssh/config
    echo "    IdentityFile ${identityfile_path}" >> ~/.ssh/config  
# handle the content of the ~/.ssh/config file
elif $(cat ~/.ssh/config | grep github.com &> /dev/null)
then
     echo "file ~/.ssh/config already has instruction for github: The script did not try to edit it."
     echo "We do expect the following lines: (please check)"
     echo "Host github.com"
     echo "    Hostname github.com"
     echo "    User git"
     echo "    IdentityFile ${identityfile_path}"
else
    echo "adding configuration for github in ssh config file"
    echo "" >> ~/.ssh/config
    echo "Host github.com" >> ~/.ssh/config
    echo "    Hostname github.com"  >> ~/.ssh/config
    echo "    User git" >> ~/.ssh/config
    echo "    IdentityFile ${identityfile_path}" >> ~/.ssh/config
fi



