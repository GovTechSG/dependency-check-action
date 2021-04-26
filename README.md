# depedency-check-action
Runs OWASP dependency-check on github repo

## Docker Image

Image [public.ecr.aws/govtechsg/cicd-images:dependency-check-latest](https://gallery.ecr.aws/govtechsg/cicd-images) used is built from [govtechsg/cicd-images](https://github.com/GovTechSG/cicd-images)

## Usage

### Github action

Basic setup for yaml

```yaml
name: OWASP Dependency Check
on:
  schedule:
    - cron:  '15 0 * * *' # Run Daily checks

jobs:
  dependency-check:
    runs-on: ubuntu-latest
    name: Dependency Check
    steps:
      - uses: actions/checkout@v2
      - uses: GovTechSG/dependency-check-action@v1.0.0
        with:
          Project-Name: ${{ github.repository }}
      - name: Upload Test results
        uses: actions/upload-artifact@master
        with:
          name: Depcheck report
          path: ${{github.workspace}}/reports
```

#### Nodejs repository

When running against a nodejs repository, remember to install your node_modules prior to running dependency check.

```yaml
name: OWASP Dependency Check
on:
  schedule:
    - cron:  '15 0 * * *' # Run Daily checks

jobs:
  dependency-check:
    runs-on: ubuntu-latest
    name: Dependency Check
    strategy:
      matrix:
        node-version: [12.x]
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - uses: GovTechSG/dependency-check-action@v1.0.0
        with:
          Project-Name: ${{ github.repository }}
      - name: Upload Test results
        uses: actions/upload-artifact@master
        with:
          name: Depcheck report
          path: ${{github.workspace}}/reports
```