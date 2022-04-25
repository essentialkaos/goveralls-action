<p align="center"><a href="#readme"><img src="https://gh.kaos.st/goveralls-action.svg"/></a></p>

<br/>

Action for sending coverage data with [goveralls](https://github.com/mattn/goveralls).

### Usage

Create file `.github/workflows/ci.yml`.

Add next code to it:

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
        id: go

      - name: Setup PATH
        run: |
          echo "GOPATH=${{ github.workspace }}" >> "$GITHUB_ENV"
          echo "GOBIN=${{ github.workspace }}/bin" >> "$GITHUB_ENV"
          echo "${{ github.workspace }}/bin" >> "$GITHUB_PATH"

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
          profile: ${{env.SRC_DIR}}/covprofile

```

### Options

| Option | Description | Value |
|--------|-------------|-------|
| `profile` | Path to coverage profile | _Path_ |
| `path` | Path to directory with sources | _Path_ |
| `shallow` | Shallow coveralls internal server errors | _Boolean_ |
| `version` | Aligo version | _Version in semver notation_ |

### License

[Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)

<p align="center"><a href="https://essentialkaos.com"><img src="https://gh.kaos.st/ekgh.svg"/></a></p>
