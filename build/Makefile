# Build and push all 45 train-ticket images to hdali00 on Docker Hub.
#
# Prerequisites:
#   - Docker Desktop or Docker Engine running locally
#   - docker login (authenticated to hub.docker.com as hdali00)
#   - HDAli00/train-ticket checked out at SRC (see below)
#
# Usage:
#   make build-all SRC=/path/to/HDAli00/train-ticket
#   make push-all
#   make build-svc-ts-order-service SRC=/path/to/HDAli00/train-ticket

REGISTRY ?= hdali00
TAG      ?= latest

# Path to the HDAli00/train-ticket source root.
# Override on the command line: make build-all SRC=~/code/train-ticket
SRC ?= $(error Set SRC to the path of the HDAli00/train-ticket checkout, e.g. make build-all SRC=~/train-ticket)

# Directory containing this Makefile (to reference the Dockerfiles regardless of cwd).
BUILD_DIR := $(dir $(abspath $(lastword $(MAKEFILE_LIST))))

# --------------------------------------------------------------------------- service list
# 44 Java microservices — one entry per service.
JAVA_SERVICES := \
  ts-auth-service \
  ts-user-service \
  ts-verification-code-service \
  ts-route-service \
  ts-contacts-service \
  ts-order-service \
  ts-order-other-service \
  ts-config-service \
  ts-station-service \
  ts-train-service \
  ts-travel-service \
  ts-travel2-service \
  ts-preserve-service \
  ts-preserve-other-service \
  ts-basic-service \
  ts-price-service \
  ts-notification-service \
  ts-security-service \
  ts-inside-payment-service \
  ts-execute-service \
  ts-payment-service \
  ts-rebook-service \
  ts-cancel-service \
  ts-assurance-service \
  ts-seat-service \
  ts-travel-plan-service \
  ts-news-service \
  ts-voucher-service \
  ts-route-plan-service \
  ts-food-service \
  ts-consign-service \
  ts-consign-price-service \
  ts-admin-basic-info-service \
  ts-admin-order-service \
  ts-admin-route-service \
  ts-admin-travel-service \
  ts-admin-user-service \
  ts-avatar-service \
  ts-delivery-service \
  ts-food-delivery-service \
  ts-gateway-service \
  ts-station-food-service \
  ts-train-food-service \
  ts-wait-order-service

ALL_IMAGES := $(JAVA_SERVICES) ts-ui-dashboard

# --------------------------------------------------------------------------- targets
.PHONY: build-all build-java build-ui push-all \
        $(addprefix build-svc-,$(ALL_IMAGES)) \
        $(addprefix push-svc-,$(ALL_IMAGES))

## Build every image (Java services + UI dashboard).
build-all: build-java build-ui

## Build all Java service images.
build-java: $(addprefix build-svc-,$(JAVA_SERVICES))

## Build the nginx/OpenResty UI dashboard image.
build-ui: build-svc-ts-ui-dashboard

## Push every image to Docker Hub.
push-all: $(addprefix push-svc-,$(ALL_IMAGES))

# Per-service build: Java microservices.
$(addprefix build-svc-,$(JAVA_SERVICES)): build-svc-%:
	@echo ">>> Building $(REGISTRY)/$*:$(TAG)"
	DOCKER_BUILDKIT=1 docker build \
		--build-arg SVC=$* \
		-t $(REGISTRY)/$*:$(TAG) \
		-f $(BUILD_DIR)Dockerfile.java-service \
		$(SRC)

# UI dashboard build (separate Dockerfile).
build-svc-ts-ui-dashboard:
	@echo ">>> Building $(REGISTRY)/ts-ui-dashboard:$(TAG)"
	DOCKER_BUILDKIT=1 docker build \
		-t $(REGISTRY)/ts-ui-dashboard:$(TAG) \
		-f $(BUILD_DIR)Dockerfile.ui-dashboard \
		$(SRC)

# Per-service push.
$(addprefix push-svc-,$(ALL_IMAGES)): push-svc-%:
	@echo ">>> Pushing $(REGISTRY)/$*:$(TAG)"
	docker push $(REGISTRY)/$*:$(TAG)

# --------------------------------------------------------------------------- helpers
.PHONY: list
## Print the full service list.
list:
	@for svc in $(ALL_IMAGES); do echo $$svc; done
