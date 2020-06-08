CURL := $(shell which curl 2> /dev/null)
TMP ?= /tmp
JQ ?= $(shell which jq 2> /dev/null)

.PHONY: jq/install
## Install jq
jq/install: %install:
	$(JQ) --version

.PHONY: jq/search
## Pipe a JSON file to jq to process filter defined in environment variable JQ_SEARCH_VAR
jq/search: %jq/search:
	@$(shell $(SELF) jq/install 1&>2 /dev/null)
	@$(JQ) $(JQ_SEARCH_VAR)
