# repos

Check all subdirectories for .git and fetch/pull from them into develop (or main/master etc.). Remember and and stash changes on the current branch.

```bash
#!/bin/bash

# Check if the SSH agent is running
if [ -z "$SSH_AUTH_SOCK" ] || [ ! -S "$SSH_AUTH_SOCK" ]; then
    echo "SSH agent not running. Starting SSH agent."
    eval "$(ssh-agent -s)"
fi

# Check if the private key is already added
if ! ssh-add -L | grep -q "your_private_key_file"; then
    echo "Adding private key to SSH agent."
    ssh-add ~/.ssh/id_ed25519_krzwierz_79_t480
fi



for dir in */; do (
  cd "$dir" &&
  if [ -d .git ]; then
    # colors from https://tldp.org/HOWTO/Bash-Prompt-HOWTO/x329.html
    echo -e "\n\n==================================================\n\e[cecking \e[32m$dir\e[0m"
    
    # Check the current branch
    current_branch=$(git rev-parse --abbrev-ref HEAD)
    
    # Check for uncommitted changes
    if [ -n "$(git status --porcelain)" ]; then
      echo -e "Warning: Uncommitted changes on branch \e[36m $current_branch\e[0m on \e[32mm$dir\e[0m. Stashing..."
      # Save changes to stash including untracked files
      git stash save --include-untracked "Stash changes before updating to develop"
    else
      echo -e "Current branch is: \e[36m$current_branch\e[0m"  
    fi

    git fetch #-v 

    # Check if 'develop' branch exists
    if git rev-parse --verify develop >/dev/null 2>&1; then
      selected_branch=develop
    elif git rev-parse --verify main >/dev/null 2>&1; then
      selected_branch=main
    elif git rev-parse --verify master >/dev/null 2>&1; then
      selected_branch=master
    else
      # List branches and prompt user to select one
      echo "No 'develop/main/master' branch in this repository."
      echo "Available branches:"
      git branch -a
      read -p "Enter the branch to check out and pull changes: " selected_branch
    fi
    
    git checkout "$selected_branch" &&
    git pull origin "$selected_branch" &&
    echo -e "Updated \e[36m$selected_branch\e[0m on \e[32m$dir\e[0m"

    # Check out the original branch
    git checkout "$current_branch"
  fi
); done
```