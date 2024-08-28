A test repository for demonstrating how to set up GitBook doc automation across multiple repositories.

## How to set up the SSH deploy keys

1. On your local machine, create an SSH key that the repositories will use to deploy.
    * You can do this via the command: `ssh-keygen -t ed25519 -C "git@github.com:<org>/<repo>.git"`
    * Replace `<org>` and `<repo>` with the actual values. For instance, this repository's org would be `mike-solomon` and the repo would be `mike-workflow-example`.
    * It will ask for a place to save the key - you'll want to save it on your own machine (so not in the repository as that would be dangerous if you uploaded it to GitHub).
    * Note that if you've already made an SSH key for GitHub, **you can't just let it have the default name**.
    * **Do not** enter a passphrase when it asks for one.
2. Open up the repository that is going to do the generation of the docs (so not the repository connected to GitBook) and go to `Settings` -> `Secrets and variables` -> `Actions`. I've done that already in this repository.
3. Click on `New repository secret` and copy your SSH key (not the public key - but the private key) into the secret. Name it something that can be easily referenced inside of a workflow file (e.g., `REWRITE_DOCS_DEPLOY_KEY`)
4. Go to the documentation repository that's managed by GitBook in GitHub and navigate to `Settings` -> `Deploy keys`.
5. Press `Add deploy key` and then paste in the public key that was generated. Give it any descriptive title you want. **Make sure to check allow write access**.
