+++
title = "Continuous Integration"
weight = 108
+++

Generally, using Hermit in CI is similar to using it locally - activate
your environment via `. ./bin/activate-hermit`, add `<repo>/bin` to your
`$PATH`, or use `./bin/hermit env` to directly update your CI environment.

## GitHub Actions

Using Hermit in GitHub Actions is straightforward. Just add the following step to each job:

```yaml
      - name: Init Hermit
        run: ./bin/hermit env --raw >> $GITHUB_ENV
```

eg.

```yaml
jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Init Hermit
        run: ./bin/hermit env --raw >> $GITHUB_ENV
      - name: Test
        run: go test ./...
```

## Jenkins

Here's an example `Jenkinsfile` to use Hermit inside [Jenkins](https://www.jenkins.io/):

```groovy
pipeline {
  agent any

  stages {
    stage('Do stuff') {
      environment {
        hermitEnvVars = sh(returnStdout: true, script: './bin/hermit env --raw').trim()
      }

      steps {
        withEnv(hermitEnvVars.split('\n').toList()) {
          // now we can use any hermit package directly...
          sh 'go build'
        }
      }
    }
  }
}
```
