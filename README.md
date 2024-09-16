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

## How to create a GitHub workflow for doc generation

1. From the repository where you'll generate docs, create a `.github/workflows` directory.
2. Inside of that directory, create a YAML file with a descriptive name such as `docs.yml`.
3. Determine how you want to trigger a documentation update. Maybe this is a new tag being added to the repository or maybe you just want to manually press a button to trigger it. [See the GitHub workflow docs for more information](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions). This trigger will go at the top of the YAML file. [In my example](https://github.com/mike-solomon/mike-workflow-example/blob/main/.github/workflows/docs.yml), my workflow will automatically run if a tag is created that starts with the letter "v". It will also let people run it manually.
4. Next, [copy what I have in the jobs section](https://github.com/mike-solomon/mike-workflow-example/blob/main/.github/workflows/docs.yml#L10-L45).
5. Replace `REWRITE_DOCS_DEPLOY_KEY` with the secret you created above.
6. Modify the `Push to rewrite-docs` section to match your needs - such as cloning your own repository or changing the file names you copy over.
   * There's a lot of flexibility here. For instance, if you wanted to update variables that matched a specific pattern, you could add a command like the following - which will look for various phrases and replace them with a newer version (example commit):

```bash
find . -name '*.md' -exec sed --in-place --regexp-extended --expression "s@CLI v?3\.[0-9]+\.[0-9]+@CLI ${tag#v}@g" --expression "s@\(Staging\) \| v?3\.[0-9]+\.[0-9]+@(Staging) | ${tag#v}@g" {} \;
```

7. Note that you can leave the git config `user.email` and `user.name` as is - those are just defaults for the github actions bot.
