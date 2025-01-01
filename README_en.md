English | [Deutsch](README.md)

# HowTo: SSH key with Windows PowerShell
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Ftoafez%2FHowTo_Windows.SSH.Powershell%2Fblob%2Fmain%2FREADME_en.md&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)


## What is this about?
The following instructions describe **how to set up SSH public key authentication** from a local Linux system to a remote Linux server **using Windows PowerShell**.

# Introduction
Secure Shell, or SSH for short, is a network protocol for establishing encrypted connections between devices on the local network or over the Internet. SSH public key authentication uses a private and public key pair to establish a password-free login to a remote server, which can be further secured by entering an additional passphrase if required. The use of such a key pair is therefore much more difficult to compromise than entering a password.

#### _Note: Text in uppercase within square brackets is a placeholder and must be replaced with your own information, but may be used in some places for information purposes only. Please note that the square brackets are part of the placeholder and must also be removed when replacing with your own information._


## To open PowerShell
1. Start **PowerShell** from the Windows programs list.
2. You should be directly in the home directory of your currently logged on Windows user.

	`PS C:\Users\[USERNAME]>`

3. Check the version of PowerShell again to be sure. It should be at least version 5.1 or higher. To do this, type the following command at a command prompt

	`$PSVersionTable.PSVersion`

    **Note:** Starting with Windows 10 1809, the OpenSSH client is installed by default. When used with Windows PowerShell 5.1 or later, SSH keys can be easily created.

## Create an SSH key pair
1. You can now start creating a new **SSH key pair** consisting of a **public** and a **private key**. This is done using the **ssh-keygen** command. By simply typing the ssh-keygen command, different encryption algorithms can be used depending on the configuration. You should therefore always specify which algorithm is to be used. In the following, the **RSA** algorithm is used with an encryption of **4096** bits.

    `ssh-keygen -t rsa -b 4096`

    _(**-t** stands for type, i.e. the type of encryption, in this case rsa for RSA protocol 2)_

    _(**-b** stands for bit, i.e. the bit length of the encryption key, in this case 4096 bits)_

2. Immediately after running the command, you will be asked for the filename to store the SSH keys in. If you want to keep the suggested path and filename, simply press Enter to confirm. Otherwise, you can enter an alternative filename without extension using the full path. In this example, the suggested filename is used.

	```
	Generating public/private rsa key pair.
	Enter file in which to save the key (C:\Users\[USERNAME]/.ssh/id_rsa):
	Created directory 'C:\\Users\\[USERNAME]/.ssh'.
	```

3. You will then be asked to enter a passphrase, i.e. a password. Using a passphrase is highly recommended as it prevents the private key from being easily copied and used, even if your client operating system has been compromised. The disadvantage of a passphrase is that you have to enter it every time you want to connect via SSH. This can be counterproductive if you later want to establish unattended and thus automated SSH connections, for example to perform automated backups. In this case, we strongly advise against entering a passphrase. It is up to you whether you want to use a passphrase or not. In this example, no passphrase is used, but the following two prompts are simply skipped by pressing Enter.
	```
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	```

	A similar issue to this one will follow...

	```
	Your identification has been saved in C:\Users\[USERNAME]/.ssh/id_rsa
	Your public key has been saved in C:\Users\[USERNAME]/.ssh/id_rsa.pub
	The key fingerprint is:
	SHA256:ScteBU5B3/m7/cod5OqlH8JSDpiNC8oGGYx0sEhoHFM [USERNAME]@[CLIENT-PC]
	The key's randomart image is:
	+---[RSA 4096]----+
	|o*+E     .=.     |
	|=o*      o o . . |
	|oo o    . . o o  |
	|    o  o o=.   . |
	|   o   .S+.o . ..|
	|    o ..... = o .|
	|     +  .. . + * |
	|    .       ..=.=|
	|            .+++=|
	+----[SHA256]-----+
	PS C:\Users\[USERNAME]>
	```

4. After execution, there will be a file called **id_rsa** containing the **private** key and a file called **id_rsa.pub** containing the **public** key in the directory `C:\Users\[USERNAME]/.ssh>`.

5. Change to this directory with **cd** _(stands for 'change directory')_.

    `cd .ssh`

6. The prompt at this point should include the .ssh directory as confirmation and look something like this

    `PS C:\Users\[USERNAME]/.ssh>`

7. The contents of the directory can be viewed with the command **dir** _(stands for 'directory')_.

    `dir`

    **Example of output**

    ```
        Directory: C:\Users\[USERNAME]\.ssh

    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    -a---          31.12.2024    07:00           3389 id_rsa
    -a---          31.12.2024    07:00            749 id_rsa.pub
    ```

    **Note:** You can always view the contents of the directory that you are currently in.

8. If required, you can display the contents of the public key by using the **type** command in the PowerShell console. If you want to share the public key with other people, you can also copy the key and save it in a separate text file.

    `type id_rsa.pub`

    **Example of output**

    ```
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCbTJMv9N+pU8n7SaDa9RLazZwB+cR0ke2ggq/Xbria3fsuLGG/wrd5mOt1KYSg7FWiiHUSec0s1sX0OAbWk8qX47UaJKe7SP6so39kVjkRGINACsojvYI773LBpDuKmGzBqsTpBSlYLGLtA7rs669YSEV+8M6Z7NJj6YySDRBL/TSx/cjiJyh5h6yHwW/+nqzFBteqzVoBQ241nGdVzcb5S16DnJLhn2MB7es70yOzq02y5XpFSPswme6sH5/jIkNhp1OU2++jPxLNOkcN73WB3pc3jz7bZxkBSh7zJxy1o39kVjkRGISbznxxbIGDn20iAFoLF6IHl0mWhiuYVwSRjZ8M1Kf71qYv7X3RHMrKQQM5qYtpk/R0NdGrvemDP/5gAI0wOG1D/RQD8nBHzHwuNvLGLtA7rs66tcfj1g+jwwMPzEgX5MsisbIKfhwLJZE34zyfWDj/Vl5n44yG7/YDkYmxtPRsYbfLUm5D+q5dhm5+g3H8t0/eQXHel7chM5I8j4pElDhFOCF8jeaCeawxAGrRn6Dab93BHF1wwowOp4kVWKw0lnvTQfbcIc5BSrxR2uYfQk9dwiiDeE6npEN11mTezRN/tsZ6JE4OrPL8oGha7Fa73AIMvyo+/kKsl2wMpBHYRZq70dp0MTUfIAQPAvNM31yNY4B8oIKV+wBxXw== [USERNAME]@[CLIENT-PC]
    ```

## Creating the authorized_keys file and adding your own public key
1. The **authorized_keys** file will later contain all the public keys of known remote servers that wish to access your client operating system to enable a passwordless SSH connection, in addition to your own key. To create the **authorized_keys** file, type the following command

    `New-Item -Path $env:USERPROFILE\.ssh\authorized_keys -ItemType File`

2. Use the following command to enter your own public key in authorized_keys.

    `type $env:USERPROFILE\.ssh\id_rsa.pub >> $env:USERPROFILE\.ssh/authorized_keys`

## Establishing an SSH connection to your remote server
1. To connect to your remote server, you need to know the IP address and port, as well as the username and password. To do this, replace the placeholders for [PORT], [USERNAME] and [IP ADDRESS] in the following command with your own data. Then run the following command

    `ssh -p [PORT] [USERNAME]@[IP-ADDRESS]`

2. After confirming the following command with the Enter key, you should see a message at the end of the text asking you to confirm the connection with **yes**. `Are you sure you want to continue the connection (yes/no/[fingerprint])?` This message only appears if you are connecting to the remote server via SSH for the first time. A **fingerprint** is stored in the `C:\Users\[USERNAME]/.ssh/known_hosts` file on your client operating system to allow future connections.

    ```
    The authenticity of host '[IP-ADRESSE] ([IP-ADRESSE])' can't be established.
    ED25519 key fingerprint is SHA256:DosguIv2tIYP+9n3BIrSM2df1841CdXbIc4pAZ01ehA.
    This key is not known by any other names.
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
    ```
    
    You will now be asked to enter your password to log in to your remote server.

    `[USERNAME]@[IP ADDRESS]'s password:`

3. Once you have successfully logged in, you should be on your remote server's console. Again check that you are in the logged-in user's home directory.
     ```
    [USERNAME]@[REMOTE-SERVER]:~$ pwd
    /home/[USERNAME]
    ```

4. Create a new hidden directory called .ssh and give it the necessary permissions.

    `mkdir .ssh`

    `chmod 700 .ssh`

6. Log out of the remote server to return to the console of your client operating system.

    `exit`

## Transfer the public key to the remote server.
1. The contents of the id_rsa.pub file, and thus the public key, can now be transferred to the remote server via an SSH connection to the authorised_keys file, which can be created if it does not yet exist. To do this, replace the placeholders for [PORT], [USERNAME] and [IP ADDRESS] in the following command with your own without using the square brackets. Then run the following command

    `type $env:USERPROFILE\.ssh\id_rsa.pub | ssh -p [PORT] [USERNAME]@[IP-ADRESS] "cat >> .ssh/authorized_keys"`

2. After successfully logging in with your password, the public key was saved to the ~/.ssh/authorized_keys file on your remote server and the connection was closed. You are still in the local PowerShell prompt.

    `PS C:\Users\[USERNAME]\.ssh>`

## Re-establish the SSH connection to your remote server...
1. You should now be able to log in to the remote server without having to enter a password. To check if this works, log in to the remote server again.

    `ssh -p [PORT] [USERNAME]@[IP-ADRESS]`

2. If everything worked, you should now be on your remote server's console without having to enter a password (unless you use one of the passphrases mentioned above). And while you're at it, you can also correct the permissions on the authorized_keys file by issuing the following command

    `chmod 600 ~/.ssh/authorized_keys`

3. You have now successfully set up passwordless SSH key authentication.
