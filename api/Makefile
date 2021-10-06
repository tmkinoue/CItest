GO ?= go
GOFMT ?= gofmt "-s"
PACKAGES ?= $(shell $(GO) list ./...)
VETPACKAGES ?= $(shell $(GO) list ./...)
GOFILES := $(shell find . -name "*.go" -type f)
TESTFOLDER := $(shell $(GO) list ./...)

.DEFAULT_GOAL := help

.PHONY: help
help: ## Print help ## make help
	@echo "Example operations by makefile."
	@echo ""
	@echo "Usage: make [SubCommand] argument_name=argument_value"
	@echo "      dry-run=(1, blank) ※not required"
	@echo ""
	@echo "Command list:"
	@echo ""
	@printf "\033[36m%-35s\033[0m %-50s %s\n" "[SubCommand]" "[Description]" "[Example]"
	@grep -E '^[/a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | perl -pe 's%^([/a-zA-Z_-]+):.*?(##)%$$1 $$2%' | awk -F " *?## *?" '{printf "\033[36m%-35s\033[0m %-50s %s\n", $$1, $$2, $$3}'

test: ## Run test
	@$(GO) test -v -cover -race $(TESTFOLDER)


compose-test: ## Run test via docker-compose
	docker-compose -f ./docker-compose.test.yaml run sample-api-test make test

vet: ## Run vet
	@$(GO) vet $(VETPACKAGES)

lint: ## Run lint
	@hash golint > /dev/null 2>&1; if [ $$? -ne 0 ]; then \
		GO111MODULE=off $(GO) get -u golang.org/x/lint/golint; \
	fi
	for PKG in $(PACKAGES); do golint -set_exit_status $$PKG || exit 1; done;

fmt: ## Run fmt
	$(GOFMT) -w $(GOFILES)

fmt-check: ## Run fmt-check
	@diff=$$($(GOFMT) -d $(GOFILES)); \
	if [ -n "$$diff" ]; then \
		echo "Please run 'make fmt' and commit the result:"; \
		echo "$${diff}"; \
		exit 1; \
	fi;

check: lint vet fmt-check test ## Run golint, vet, fmt-check, test

compose-check: ## Run make check via docker-compose
	docker-compose -f ./docker-compose.test.yaml run sample-api-test
