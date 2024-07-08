# Setting Up Multiple Git Accounts on Windows and use of VSCode

## Introduction
Managing multiple Git accounts and ensuring a smooth push and pull process in VSCode involves configuring SSH keys and setting up Git properly. This guide will help you configure everything to avoid authentication dialog boxes when using `git push` from VSCode.

## Step 1: Install Git
Ensure Git is installed on your Windows machine. If not, download and install it from [git-scm.com](https://git-scm.com/).

## Step 2: Generate SSH Keys
Generate SSH keys for each of your Git accounts if you haven't already done so.

### For Account 1
```sh
ssh-keygen -t rsa -b 4096 -C "your_email_account1@example.com"
```
- Save the key as `id_rsa_account1` when prompted.
- Add the SSH key to the ssh-agent:
  ```sh
  eval $(ssh-agent -s)
  ssh-add ~/.ssh/id_rsa_account1
  ```

### For Account 2
```sh
ssh-keygen -t rsa -b 4096 -C "your_email_account2@example.com"
```
- Save the key as `id_rsa_account2` when prompted.
- Add the SSH key to the ssh-agent:
  ```sh
  eval $(ssh-agent -s)
  ssh-add ~/.ssh/id_rsa_account2
  ```

## Step 3: Add SSH Keys to GitHub
Copy the SSH keys to your clipboard and add them to the respective GitHub accounts.

### For Account 1
```sh
clip < ~/.ssh/id_rsa_account1.pub
```
- Go to GitHub Account 1 > Settings > SSH and GPG keys > New SSH key and paste the key.

### For Account 2
```sh
clip < ~/.ssh/id_rsa_account2.pub
```
- Go to GitHub Account 2 > Settings > SSH and GPG keys > New SSH key and paste the key.

## Step 4: Configure SSH Config
Configure SSH to use different keys for different accounts.

Edit the `config` file in the `.ssh` directory:
```sh
notepad ~/.ssh/config
```

Add the following configuration:
```sh
# Account 1
Host github-account1
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_account1

# Account 2
Host github-account2
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_account2
```

## Step 5: Configure Git in VSCode
Ensure Git in VSCode is using SSH for authentication.

### Global Configuration
```sh
git config --global user.name "Your Name"
git config --global user.email "your_global_email@example.com"
```

### Repository-Specific Configuration
Navigate to your repository and set the specific configurations:
```sh
cd path/to/your/repository
```

#### For Account 1 Repository
```sh
git config user.name "Your Name Account 1"
git config user.email "your_email_account1@example.com"
git remote set-url origin git@github-account1:username/repo.git
```

#### For Account 2 Repository
```sh
git config user.name "Your Name Account 2"
git config user.email "your_email_account2@example.com"
git remote set-url origin git@github-account2:username/repo.git
```

## Step 6: Ensure VSCode Uses SSH
Make sure VSCode uses SSH for Git operations:

1. Open your project in VSCode.
2. Go to the Command Palette (Ctrl+Shift+P) and type `Git: Add Remote`.
3. Set the remote URL using the SSH URL:
   - For Account 1: `git@github-account1:username/repo.git`
   - For Account 2: `git@github-account2:username/repo.git`

## Step 7: Avoid Dialog Boxes for Git Push
To avoid the authentication dialog box when pushing from VSCode:

1. **Check SSH Agent**: Ensure the SSH agent is running and the keys are added.
   ```sh
   eval $(ssh-agent -s)
   ssh-add ~/.ssh/id_rsa_account1
   ssh-add ~/.ssh/id_rsa_account2
   ```

2. **Configure VSCode Settings**: Add the following to your VSCode settings to ensure it uses the SSH agent:
   - Open `settings.json` (File > Preferences > Settings > Search for `settings.json`).
   - Add the following:
     ```json
     "terminal.integrated.env.windows": {
         "SSH_AUTH_SOCK": "\\\\.\\pipe\\ssh-agent"
     }
     ```

3. **Start SSH Agent Automatically**:
   - Add the following to your `~/.bashrc` or `~/.bash_profile` to automatically start `ssh-agent` and add keys on shell startup:
     ```sh
     eval $(ssh-agent -s)
     ssh-add ~/.ssh/id_rsa_account1
     ssh-add ~/.ssh/id_rsa_account2
     ```

4. **Verify SSH Connection**:
   - Test your SSH connection to GitHub:
     ```sh
     ssh -T git@github-account1
     ssh -T git@github-account2
     ```

## Troubleshooting and Common Errors

### Error: Permission Denied (publickey)
**Cause:** SSH keys are not added to the ssh-agent or not configured correctly in the SSH config file.
**Solution:** Ensure the SSH keys are added to the ssh-agent and the SSH config file is correctly configured.
```sh
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa_account1
ssh-add ~/.ssh/id_rsa_account2
```

### Error: Host Key Verification Failed
**Cause:** The SSH key for the remote host is not verified.
**Solution:** Remove the old host key and connect again to save the new host key.
```sh
ssh-keygen -R github.com
ssh -T git@github-account1
ssh -T git@github-account2
```

### Error: Multiple SSH Key Identities in Use
**Cause:** Git might not use the correct SSH key if multiple identities are loaded.
**Solution:** Use the `IdentityFile` configuration in the SSH config file to specify the correct key for each account.

### Error: Cannot Read Property 'email' of Undefined
**Cause:** The user email is not set correctly in the repository configuration.
**Solution:** Set the user email in the repository configuration.
```sh
git config user.email "your_email_account1@example.com"
```

## References
- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub SSH Key Setup](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)
- [VSCode Git Integration](https://code.visualstudio.com/docs/editor/versioncontrol)

