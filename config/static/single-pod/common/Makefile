include ../../../../variables.mk
include ../../../../binaries.mk

SINGLEPOD_BASEDOMAIN := $(shell oc get dnses.config.openshift.io/cluster -o json | jq -r .spec.baseDomain )
SINGLEPOD_REALM := APPS.$(shell tr '[:lower:]' '[:upper:]' <<< "$(SINGLEPOD_BASEDOMAIN)" )
SINGLEPOD_HOSTNAME := $(OCP_NAMESPACE).apps.$(SINGLEPOD_BASEDOMAIN)
SINGLEPOD_CA_SUBJECT := CN=freeipa-$(shell date +%Y%m%d%H%M%S ), O=$(SINGLEPOD_REALM)
SINGLEPOD_ENV_FILE := env.properties

SINGLEPOD_SECRETS_FILE := secrets.properties
ifeq (,$(SINGLEPOD_PASSWORD)$(SINGLEPOD_ADMIN_PASSWORD)$(SINGLEPOD_DS_PASSWORD))
$(error "SINGLEPOD_PASSWORD or SINGLEPOD_ADMIN_PASSWORD and SINGLEPOD_DS_PASSWORD are required for generating the secret with the password; you can set it before call 'make'")
endif
# If SINGLEPOD_PASSWORD is set, try to set default value for
# empty password variables
ifneq (,$(SINGLEPOD_PASSWORD))
SINGLEPOD_ADMIN_PASSWORD ?= $(SINGLEPOD_PASSWORD)
SINGLEPOD_DS_PASSWORD ?= $(SINGLEPOD_PASSWORD)
endif
ifeq (,$(SINGLEPOD_ADMIN_PASSWORD))
$(error "SINGLEPOD_ADMIN_PASSWORD" is empty; try to set SINGLEPOD_ADMIN_PASSWORD=my-admin-pass")
endif
ifeq (,$(SINGLEPOD_DS_PASSWORD))
$(error "SINGLEPOD_DS_PASSWORD" is empty; try to set SINGLEPOD_DS_PASSWORD=my-ds-pass")
endif

.PHONY: configure
configure:
	@true > $(SINGLEPOD_ENV_FILE)
	@echo "IPA_SERVER_NAMESPACE=$(OCP_NAMESPACE)" >> $(SINGLEPOD_ENV_FILE)
	@echo "IPA_SERVER_REALM=$(SINGLEPOD_REALM)" >> $(SINGLEPOD_ENV_FILE)
	@echo "IPA_SERVER_CA_SUBJECT=$(SINGLEPOD_CA_SUBJECT)" >> $(SINGLEPOD_ENV_FILE)
	@echo "IPA_SERVER_HOSTNAME=$(SINGLEPOD_HOSTNAME)" >> $(SINGLEPOD_ENV_FILE)
	@true > $(SINGLEPOD_SECRETS_FILE)
	@echo "IPA_SERVER_PASSWORD=$(SINGLEPOD_PASSWORD)" >> $(SINGLEPOD_SECRETS_FILE)
	@echo "IPA_SERVER_ADMIN_PASSWORD=$(SINGLEPOD_ADMIN_PASSWORD)" >> $(SINGLEPOD_SECRETS_FILE)
	@echo "IPA_SERVER_DS_PASSWORD=$(SINGLEPOD_DS_PASSWORD)" >> $(SINGLEPOD_SECRETS_FILE)
	@$(KUSTOMIZE) edit set namespace $(OCP_NAMESPACE)

