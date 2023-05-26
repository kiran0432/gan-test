.DEFAULT_GOAL := default

clean:
	@rm ./bin/*

default: bin_dir install-deps build 

build: install-deps
	@go build -o bin/login ./cmd/login
	@go build -o bin/token ./cmd/token

bin_dir:
	@mkdir -p ./bin

install-deps: install-goimports

install-goimports:
	@if [ ! -f ./goimports ]; then \
		cd ~ && go get -u golang.org/x/tools/cmd/goimports; \
	fi

.PHONY: clean build %
