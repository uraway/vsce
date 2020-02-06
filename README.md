# VSCE orb

An orb for VS Code extension authors.

## Personal Access Token

To publish your extension, you need to create [Personal Access Token](https://docs.microsoft.com/azure/devops/integrate/get-started/authentication/pats).
More detail [here](https://code.visualstudio.com/api/working-with-extensions/publishing-extension#publishing-extensions).

After creation of Personal Acces Token, set as environment variable "VSCODE_MARKETPLACE_TOKEN".

## Usage

```yaml
usage:
  version: 2.1

  orbs:
    vsce: circleci/vsce@x.y.z

  jobs:
    test:
      executor: vsce/node-browsers
      steps:
        - run: npm test

  workflows:
    lint-test-publish:
      jobs:
        - test
        - vsce/publish:
            publish-token-variable: VSCODE_MARKETPLACE_TOKEN
            push-git-tag: false
            # steps for caching
            pre-install-steps:
              - restore_cache:
                  keys:
                    - v1-node-cache-{{ .Branch }}-{{ checksum "package-lock.json" }}
                    - v1-node-cache-{{ .Branch }}
                    - v1-node-cache-
            post-install-steps:
              - save_cache:
                  key: v1-node-cache-{{ .Branch }}-{{ checksum "package-lock.json" }}
                  paths:
                    - node_modules
            requires:
              - test
            filters:
              branches:
                only: master
```

Or if you prefer yarn instead of npm:

```yaml
usage:
  version: 2.1

  orbs:
    vsce: circleci/vsce@x.y.z

  jobs:
    test:
      executor: vsce/node-browsers
      steps:
        - run: npm test

  workflows:
    lint-test-publish:
      jobs:
        - test
        - vsce/publish:
            publish-token-variable: VSCODE_MARKETPLACE_TOKEN
            push-git-tag: false
            # Use yarn instead of npm
            prefer-yarn: true
            # Update checksum template
            pre-install-steps:
              - restore_cache:
                  keys:
                    - v1-node-cache-{{ .Branch }}-{{ checksum "yarn.lock" }}
                    - v1-node-cache-{{ .Branch }}
                    - v1-node-cache-
            post-install-steps:
              - save_cache:
                  key: v1-node-cache-{{ .Branch }}-{{ checksum "yarn.lock" }}
                  paths:
                    - node_modules
            requires:
              - test
            filters:
              branches:
                only: master
```
