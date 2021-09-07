
# Set the container runtime interface
ifneq (,$(shell bash -c "command -v podman 2>/dev/null"))
DOCKER ?= podman
else
ifneq (,$(shell bash -c "command -v docker 2>/dev/null"))
DOCKER ?= docker
else
ifeq (,$(DOCKER))
$(error DOCKER is not set)
endif
endif
endif

# linux
ifneq (,$(shell bash -c "command -v xdg-open" 2>/dev/null))
OPEN := xdg-open
else
# macos
	ifneq (,$(shell bash -c "command -v open" 2>/dev/null))
OPEN := open
	else
OPEN := echo Open
	endif
endif

FREEIPA_containers=init-container-uuid init-uid-gid-base main

CONTAINERS="$(FREEIPA_containers)"
# Change DOCKER_BASE_IMAGE on your pipeline settings to point to your upstream
ifeq (,$(DOCKER_BASE_IMAGE))
$(error DOCKER_BASE_IMAGE is not defined. Example DOCKER_BASE_IMAGE=quay.io/username)
endif
DOCKER_TAG ?= dev-$(shell git rev-parse --short HEAD)
DOCKER_IMAGE ?= $(DOCKER_BASE_IMAGE)/freeipa-openshift-container:$(DOCKER_TAG)


default: help


.PHONY: .FORCE
.FORCE:

.PHONY: help
help: .FORCE
	@cat HELP

.PHONY: dump-vars
dump-vars:
	@echo DOCKER=$(DOCKER)
	@echo DOCKER_BASE_IMAGE=$(DOCKER_BASE_IMAGE)
	@echo DOCKER_IMAGE=$(DOCKER_IMAGE)

# Check DOCKER_IMAGE is not empty
.PHONY: .check-docker-image-not-empty
ifeq (,$(DOCKER_IMAGE))
.check-docker-image-not-empty: .FORCE
	@echo "'DOCKER_IMAGE' must be defined. Eg: 'export DOCKER_IMAGE=quay.io/myusername/freeipa-server:latest'"
	@exit 1
else
.check-docker-image-not-empty:
endif

# Build the container image
.PHONY: container-build
container-build: .check-docker-image-not-empty Dockerfile
	$(DOCKER) build -t $(DOCKER_IMAGE) -f Dockerfile .

# Push the container image to the container registry
.PHONY: container-push
container-push: .check-docker-image-not-empty .FORCE
	$(DOCKER) push $(DOCKER_IMAGE)

# Remove container image from the local storage
.PHONY: .container-remove
.container-remove: .check-docker-image-not-empty .FORCE
	$(DOCKER) image rm $(DOCKER_IMAGE)

