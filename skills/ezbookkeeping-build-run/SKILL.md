---
name: ezbookkeeping-build-run
description: Build and run ezBookkeeping on a MacBook, especially with Docker mapped to localhost:9090, and verify the web UI in a browser. Use when Codex is asked to compile, containerize, launch, smoke-test, or troubleshoot this repository locally.
---

# ezBookkeeping Build and Run

## Docker Workflow

1. Work from the ezBookkeeping repository root.
2. Build a local Docker image with a stable tag:

```bash
./build.sh docker -t ezbookkeeping:local
```

3. Create local persistent directories, then run it on host port `9090` with local data mounted:

```bash
mkdir -p data storage log
docker run --rm --name ezbookkeeping-local \
  -p 9090:8080 \
  -v "$PWD/data:/ezbookkeeping/data" \
  -v "$PWD/storage:/ezbookkeeping/storage" \
  -v "$PWD/log:/ezbookkeeping/log" \
  ezbookkeeping:local
```

4. Verify the app at `http://localhost:9090/`.

## Source Package Fallback

Use this only when the user explicitly asks for a non-Docker run:

```bash
./build.sh package -o ezbookkeeping.tar.gz
cp conf/ezbookkeeping.ini package/ezbookkeeping-9090.ini
perl -0pi -e 's/http_port = 8080/http_port = 9090/' package/ezbookkeeping-9090.ini
cd package
./ezbookkeeping --conf-path ezbookkeeping-9090.ini server run
```

## Notes

- Docker is the preferred local run path on this MacBook.
- The container listens on `8080`; publish it as `9090:8080` so the browser URL is `http://localhost:9090/`.
- Mount `data/` by default so the container uses the local `data/ezbookkeeping.db` database instead of an empty ephemeral database.
- Mount `storage/` for uploaded files and `log/` for local logs.
- If `ezbookkeeping-local` already exists, stop or remove that container before starting a new one.
- If build or startup fails, report the failing command and relevant error lines.
