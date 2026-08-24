# Migrating a Bitbucket repository to GitHub

Before GitHub was acquired by Microsoft, Bitbucket was a great alternative for free-tier users. I moved from GitHub to Bitbucket largely because of Bitbucket's attractive free individual plan. 

Following Macrohard's acquisition, GitHub expanded considerably beyond repository hosting, adding capabilities such as GitHub Actions, cloud development, and security tools. Later, GitHub Copilot helped kick off the explosion of generative AI coding with seamless integration directly into developers' IDE workflows. 

GitHub has built its ecosystem around repositories, while Bitbucket has become tightly integrated with Atlassian's broader ecosystem. GitHub is once again the better fit unless there is a strong reason to remain within the Atlassian ecosystem. It is time to move back. I may still miss [Sourcetree](https://www.sourcetreeapp.com). 

<!-- Looking forward, new alternatives may also be worth exploring, particularly platforms such as [Cursor Origin](https://cursor.com/origin) that are being designed around AI agents. -->

Assume the source repository is hosted under a Bitbucket account/workspace/repository named `username`/`workspace`/`repo`. The repository will be migrated to `username`/`repo` on GitHub. The overall migration workflow is as follows:

```text
Bitbucket repo
       ↓
local bare clone
       ↓
empty GitHub repo
       ↓
git push --mirror
       ↓
verify the branches
       ↓
remove local clone
```

## Migration steps

1. [Create an Atlassian API token](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/).

2. Make a fresh bare clone of the source Bitbucket repository in a local directory. The Bitbucket (Atlassian) API token will be needed when prompted for credentials.

    ```bash
    git clone --bare https://username@bitbucket.org/workspace/repo.git
    cd repo.git
    ```

3. (Optional) Replace old deactivated university email address with a GitHub account associated email using [git filter-repo](https://github.com/newren/git-filter-repo).

    ```bash
    # Identify historical emails
    git log --all --format='%an <%ae>' | sort -u
    # Rewrite personal email address
    git filter-repo --email-callback 'return b"aaaa@bbbb.edu" if email == b"xxxx@yyyy.zzz" else email'
    ```

4. Authenticate GitHub CLI `gh auth login` or [create a GitHub personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).

5. Create a new destination repository on GitHub.

    ```bash
    cd ..
    # Create a private repository (use --public instead for a public repository)
    gh repo create username/repo --private
    ```

6. Push the locally cloned repository to GitHub. Authenticate using either the authenticated gh or the GitHub personal access token when prompted for credentials.

    ```bash
    $ cd repo.git
    $ git push --force --mirror https://github.com/username/repo.git
    ```

7. Verify the migration.

    ```bash
    git ls-remote https://github.com/username/repo.git
    ```

8. Clean up the local clone.

    ```bash
    cd ..
    rm -rf repo.git
    ```


