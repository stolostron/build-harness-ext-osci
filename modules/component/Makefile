# The bin of commands to run by default
COMPONENT_TYPE ?= component

# Full path to this modules' scripts bin directory.
COMPONENT_SCRIPTS_PATH ?= ${BUILD_HARNESS_EXTENSIONS_PATH}//modules/component/bin/${COMPONENT_TYPE}

# Find component name and component version from repo artifacts
COMPONENT_NAME ?= $(shell cat COMPONENT_NAME 2> /dev/null)
COMPONENT_VERSION ?= $(shell cat COMPONENT_VERSION 2> /dev/null)
COMPONENT_TAG_EXTENSION ?= -${PULL_BASE_SHA}

# Build the details for the remote destination repo for the image
COMPONENT_DOCKER_REPO ?= quay.io/${PIPELINE_MANIFEST_QUAY_ORG}

# Variables Requred by different component types:
#---helmoperator---#
IMAGE_DEPLOYED_NAME ?=

# The command to run to execute a build
COMPONENT_INIT_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/install-dependencies.sh
# The command to run to execute a build
COMPONENT_BUILD_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/build.sh
# The command to run to execute unit tests
COMPONENT_UNIT_TEST_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/run-unit-tests.sh
# The command to run to execute functional tests
COMPONENT_FUNCTIONAL_TEST_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/run-functional-tests.sh
# The command to run to execute e2e tests
COMPONENT_E2E_TEST_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/run-e2e-tests.sh
# The command to run to deploy the newly built component
COMPONENT_DEPLOY_COMMAND ?= ${COMPONENT_SCRIPTS_PATH}/deploy-to-cluster.sh

.PHONY: component/init
## Install build dependencies
component/init: %init:
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/init
	${COMPONENT_INIT_COMMAND}

.PHONY: component/build
## Build the component
component/build: %build: %init
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/build
	${COMPONENT_BUILD_COMMAND} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Built the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"

.PHONY: component/test/unit
## Execute component's unit tests
component/test/unit: %test/unit:
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/test/unit
	${COMPONENT_UNIT_TEST_COMMAND} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Run Unit Tests on the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"

.PHONY: component/test/functional
## Execute component's functional tests
component/test/functional: %test/functional:
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/test/functional
	${COMPONENT_FUNCTIONAL_TEST_COMMAND} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Run FUNCTIONAL Tests on the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"

.PHONY: component/test/e2e
## Execute component's e2e tests
component/test/e2e: %test/e2e:
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/test/e2e
	${COMPONENT_E2E_TEST_COMMAND} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Run E2E Tests on the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"


.PHONY: component/deploy
## Deploy the component
component/deploy: %deploy:
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	@echo component/deploy
	@$(SELF) oc/install
	${COMPONENT_DEPLOY_COMMAND} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION} ${OC} ${BUILD_HARNESS_PATH}/../
	@echo "Deployed the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"


.PHONY: component/push
## Push the component to COMPONENT_DOCKER_REPO
component/push: %push:
	$(call assert-set,DOCKER_USER)
	$(call assert-set,DOCKER_PASS)
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	$(DOCKER) login ${COMPONENT_DOCKER_REPO} -u ${DOCKER_USER} -p ${DOCKER_PASS}
	$(DOCKER) push ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Pushed the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"

.PHONY: component/tag
## Tag the component into COMPONENT_NEWTAG
component/tag: %tag:
	$(call assert-set,DOCKER_USER)
	$(call assert-set,DOCKER_PASS)
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	$(call assert-set,COMPONENT_NEWTAG)
	$(DOCKER) login ${COMPONENT_DOCKER_REPO} -u ${DOCKER_USER} -p ${DOCKER_PASS}
	$(DOCKER) tag ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION} ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_NEWTAG}
	@echo "Tagged the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION} as ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_NEWTAG}"

.PHONY: component/pull
## pull the component from COMPONENT_DOCKER_REPO
component/pull: %pull:
	$(call assert-set,DOCKER_USER)
	$(call assert-set,DOCKER_PASS)
	$(call assert-set,COMPONENT_NAME)
	$(call assert-set,COMPONENT_VERSION)
	$(DOCKER) login ${COMPONENT_DOCKER_REPO} -u ${DOCKER_USER} -p ${DOCKER_PASS}
	$(DOCKER) pull ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}
	@echo "Pulled the following image: ${COMPONENT_DOCKER_REPO}/${COMPONENT_NAME}:${COMPONENT_VERSION}${COMPONENT_TAG_EXTENSION}"

