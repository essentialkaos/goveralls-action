<p align="center"><a href="#readme"><img src="https://gh.kaos.st/goveralls-action.svg"/></a></p>

<br/>

Action for sending coverage data with [goveralls](https://github.com/mattn/goveralls).

### Usage

Basic usage example:

```yml
name: CI

on:
  push:
    branches: [master, develop]
  pull_request:
    branches: [master]

jobs:
  Go:
    name: Go
    runs-on: ubuntu-latest

    env:
      SRC_DIR: src/github.com/${{ github.repository }}

    steps:
      - name: Set up Go
        uses: actions/setup-go@v3
        with:
          go-version: '1.18.x'

      - name: Checkout
        uses: actions/checkout@v3
        with:
          path: ${{env.SRC_DIR}}

      - name: Run tests
        working-directory: ${{env.SRC_DIR}}
        run: go test -race -covermode atomic -coverprofile=covprofile ./...

      - name: Send coverage data
        uses: essentialkaos/goveralls-action@v1
        env:
          COVERALLS_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          path: ${{env.SRC_DIR}}
          profile: covprofile
```

Parallel tests example:

```yml
name: CI

on:
  push:
    branches: [master, develop]
  pull_request:
    branches: [master]

jobs:
  Go:
    name: Go
    runs-on: ubuntu-latest

    env:
      SRC_DIR: src/github.com/${{ github.repository }}

    strategy:
      matrix:
        go: [ '1.16.x', '1.17.x', '1.18.x' ]

    steps:
      - name: Set up Go
        uses: actions/setup-go@v3
        with:
          go-version: ${{ matrix.go }}

      - name: Checkout
        uses: actions/checkout@v3
        with:
          path: ${{env.SRC_DIR}}

      - name: Run tests
        working-directory: ${{env.SRC_DIR}}
        run: go test -race -covermode atomic -coverprofile=covprofile ./...

      - name: Send coverage data
        uses: essentialkaos/goveralls-action@v1
        env:
          COVERALLS_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          path: ${{env.SRC_DIR}}
          profile: covprofile
          parallel: true
          flag-name: linux-${{ matrix.go }}

  SendCoverage:
    name: Send Coverage
    runs-on: ubuntu-latest

    needs: Go

    steps:
      - name: Finish parallel tests
        uses: essentialkaos/goveralls-action@v1
        env:
          COVERALLS_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          parallel-finished: true
```

### Options

| Option | Description | Value |
|--------|-------------|-------|
| `profile` | Path to coverage profile | _Path_ |
| `path` | Path to directory with sources | _Path_ |
| `parallel` | Submit as parallel | _Boolean_ |
| `parallel-finished` | Mark parallel test as finished | _Boolean_ |
| `flag-name` | Job flag name (will be shown in the Coveralls UI) | _String_ |
| `shallow` | Shallow coveralls internal server errors | _Boolean_ |
| `version` | Aligo version | _Version in semver notation_ |

### License

[Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)

<p align="center"><a href="https://essentialkaos.com"><img src="https://gh.kaos.st/ekgh.svg"/></a></p>
