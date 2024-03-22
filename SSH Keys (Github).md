## Checking for existing SSH keys
In terminal check if there are any files in the `.ssh` directory
```shell
ls -al ~/.ssh
```

This should list all public SSH key files. By default, filenames of supported public keys for GitHub are one of the following.
- _`id_rsa.pub`_
- _`id_ecdsa.pub`_
- _`id_ed25519.pub`_

> [!tip+] Tip:
> If you receive an error that _~/.ssh_ doesn't exist, you do not have an existing SSH key pair in the default location.

You can now either generate a new key or upload an existing key listed in that directory. 

## Generating a new SSH key and adding it to the ssh-agent
Once again in your terminal run the following command replacing the example email with you GitHub email:
```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```
and for legacy systems that do not support the Ed25519 algorithm:
```shell
 ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
This creates a new SSH key, using the provided email as a label. 

Follow the prompts in the terminal and press enter to accept the default file location. If an SSH key was created previously, ssh-keygen may ask to rewrite another key, instead create a custom-names ssh key.  If that is the case replace the default file location and replace id_ALGORITHM with your custom key name (`/home/YOU/.ssh/id_ALGORITHM`).

You will then be asked to enter a secure passphrase.

The ssh key is now ready to be added to the ssh-agent. Start the ssh-agent in the background
```shell
eval "$(ssh-agent -s)"
```
Depending on your environment, you may need to use a different command. For example, you may need to use root access by running `sudo -s -H` before starting the ssh-agent, or you may need to use `exec ssh-agent bash` or `exec ssh-agent zsh` to run the ssh-agent.

We will now add our SSH key to the ssh-agent. Note that if you created your key with a different name, or if you are adding an existing key that has a different name, replace _id_ed25519_ in the command with the name of your private key file.
```shell
ssh-add ~/.ssh/id_ed25519
```

## Adding a new SSH key to your GitHub account
Copy the SSH public key by printing it out in your terminal and selecting it. Do not add any newlines or whitespaces:
```shell
cat ~/.ssh/id_ed25519.pub
```
If your SSH key public key file has a different name, adjust the example code accordingly.

Now in your GitHub account go into your **Settings** page. In the **Access** section of the sidebar, select the **SSH and GPG keys**. Click **New SSH key** or **Add SSH key**. In the "Title" field, add a descriptive label for the new key. For example, if you're using a personal laptop, you might call this key "Personal laptop". Select the type of key, either authentication or signing. In the "Key" field, paste your public key. Click **Add SSH key**.