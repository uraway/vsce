# Usage

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
