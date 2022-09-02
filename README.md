# About

Modified `poco` recipe

## EXTRA FEATURES

- supports "openssl/1.1.1-stable@conan/stable"

# About

## Docker build

```bash
export MY_IP=$(ip route get 8.8.8.8 | sed -n '/src/{s/.*src *\([^ ]*\).*/\1/p;q}')
sudo -E docker build \
    --build-arg PKG_NAME=poco/1.10.1 \
    --build-arg PKG_CHANNEL=dev/stable \
    --build-arg PKG_UPLOAD_NAME=poco/1.10.1@dev/stable \
    --build-arg CONAN_EXTRA_REPOS="conan-local http://$MY_IP:8081/artifactory/api/conan/conan False" \
    --build-arg CONAN_EXTRA_REPOS_USER="user -p password1 -r conan-local admin" \
    --build-arg CONAN_INSTALL="conan install --profile clang --build missing" \
    --build-arg CONAN_CREATE="conan create --profile clang --build missing" \
    --build-arg CONAN_UPLOAD="conan upload --all -r=conan-local -c --retry 3 --retry-wait 10 --force" \
    --build-arg BUILD_TYPE=Debug \
    -f conan_poco.Dockerfile --tag conan_poco . --no-cache
```

## Local build

```bash
export VERBOSE=1
export CONAN_REVISIONS_ENABLED=1
export CONAN_VERBOSE_TRACEBACK=1
export CONAN_PRINT_RUN_COMMANDS=1
export CONAN_LOGGING_LEVEL=10

export PKG_NAME=poco/1.10.1@dev/stable
conan remove --force $PKG_NAME
# NOTE: change `build_type=Debug` to `build_type=Release` in production
conan create . dev/stable -s build_type=Debug --profile clang --build missing
conan upload $PKG_NAME --all -r=conan-local -c --retry 3 --retry-wait 10 --force

# clean build cache
conan remove "*" --build --force
```

## How to diagnose errors in conanfile (CONAN_PRINT_RUN_COMMANDS)

```bash
export VERBOSE=1
export CONAN_REVISIONS_ENABLED=1
export CONAN_VERBOSE_TRACEBACK=1
export CONAN_PRINT_RUN_COMMANDS=1
export CONAN_LOGGING_LEVEL=10

# NOTE: about `--keep-source` see https://bincrafters.github.io/2018/02/27/Updated-Conan-Package-Flow-1.1/
conan create . conan/stable -s build_type=Debug --profile clang --build missing --keep-source

# clean build cache
conan remove "*" --build --force
```
