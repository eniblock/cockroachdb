VERSION 0.6

ARG helmhelper=github.com/eniblock/build/helm:develop
IMPORT $helmhelper AS helm

ARG deployhelper=github.com/eniblock/build/deploy:develop
IMPORT $deployhelper AS deploy

ARG repository=ghcr.io/eniblock/cockroachdb

deps:
    DO helm+BASE
    DO helm+UPDATE --helmdir=./cockroachdb

lint:
    FROM +deps
    COPY cockroachdb ./
    RUN lint cockroachdb ./ --values values-dev.yaml

build:
    FROM +deps
    ARG tag=0.1.0-local
    DO helm+BUILD --helmdir=./cockroachdb --tag=${tag}
    SAVE ARTIFACT /app/*-${tag}.tgz

save-package:
    FROM scratch
    COPY +build/*.tgz /
    SAVE ARTIFACT /*.tgz AS LOCAL ./

publish:
    ARG tag=0.1.0-local
    FROM +build --tag=${tag}
    DO helm+LOGIN
    DO helm+PUBLISH --repository=$repository
