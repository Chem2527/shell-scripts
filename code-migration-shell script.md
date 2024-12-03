
#!/bin/bash

# Bitbucket credentials
BITBUCKET_USERNAME="fe-be-admin"
BITBUCKET_APP_PASSWORD=""
BITBUCKET_WORKSPACE="fe-be1"

# GitHub credentials
GITHUB_USERNAME="Chem2527"  # This is your personal GitHub username
GITHUB_TOKEN=""  # Your GitHub personal access token

# List of repositories you want to migrate (or get from Bitbucket API)
REPOS=("demo" "repo2" "repo3")  # Update this list as needed

for REPO in "${REPOS[@]}"; do
    echo "Migrating repository: $REPO"

    # Log the URL being used for cloning
    CLONE_URL="https://$BITBUCKET_USERNAME@bitbucket.org/$BITBUCKET_WORKSPACE/$REPO.git"
    echo "Cloning repository: $CLONE_URL"

    # Clone the repository
    git clone "$CLONE_URL"

    if [ $? -ne 0 ]; then
        echo "Failed to clone $REPO from Bitbucket. Skipping..."
        continue
    fi

    cd "$REPO" || exit

    # Add GitHub as a remote
    git remote add github "https://$GITHUB_USERNAME:$GITHUB_TOKEN@github.com/$GITHUB_USERNAME/$REPO.git"

    # Push all branches and tags to GitHub
    git push --mirror github

    cd ..
    rm -rf "$REPO"  # Clean up
done
