# VSCE orb

An orb for VS Code extension authors.

## Personal Access Token

To publish your extension, you need to create [Personal Access Token](https://docs.microsoft.com/azure/devops/integrate/get-started/authentication/pats).
More detail [here](https://code.visualstudio.com/api/working-with-extensions/publishing-extension#publishing-extensions).

After creation of Personal Acces Token, set as environment variable "VSCODE_MARKETPLACE_TOKEN".

## Usage

Full usage example: [Orb registry page](https://circleci.com/orbs/registry/orb/uraway/vsce)

```yaml
version: 2.1

orbs:
  vsce: uraway/vsce@x.y.z

jobs:
  test:
    executor: vsce/node-browsers
    steps:
      - checkout

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
version: 2.1

orbs:
  vsce: uraway/vsce@x.y.z

jobs:
  test:
    executor: vsce/node-browsers
    steps:
      - checkout

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
