 cat sample.sh
#!/bin/bash


BITBUCKET_USERNAME="fe-be-admin"
BITBUCKET_APP_PASSWORD="F"  # Bitbucket credentials
BITBUCKET_WORKSPACE="fe-be1"


GITHUB_USERNAME="Chem2527"  # This is your personal GitHub username   # GitHub credentials
GITHUB_TOKEN="gq"  # Your GitHub personal access token


REPOS=("demo" "repo2" "repo3")  # Update this list as needed # List of repositories you want to migrate (or get from Bitbucket API)

for REPO in "${REPOS[@]}"; do
    echo "Migrating repository: $REPO"

   
    CLONE_URL="https://$BITBUCKET_USERNAME@bitbucket.org/$BITBUCKET_WORKSPACE/$REPO.git"    # Log the URL being used for cloning
    echo "Cloning repository: $CLONE_URL"


    git clone "$CLONE_URL"       # Clone the repository

    if [ $? -ne 0 ]; then
        echo "Failed to clone $REPO from Bitbucket. Skipping..."
        continue
    fi

    cd "$REPO" || exit

 
    git remote add github "https://$GITHUB_USERNAME:$GITHUB_TOKEN@github.com/$GITHUB_USERNAME/$REPO.git"      # Add GitHub as a remote

  
    git push --mirror github     # Push all branches and tags to GitHub

    cd ..
    rm -rf "$REPO"  # Clean up
done
