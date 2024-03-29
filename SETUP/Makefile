.PHONY: help login init set-cors destroy gen-key
.DEFAULT_GOAL := help

define PRINT_HELP_PYSCRIPT
import re, sys

for line in sys.stdin:
	match = re.match(r'^([a-zA-Z_-]+):.*?## (.*)$$', line)
	if match:
		target, help = match.groups()
		print("%-20s %s" % (target, help))
endef
export PRINT_HELP_PYSCRIPT

help:
	@python -c "$$PRINT_HELP_PYSCRIPT" < $(MAKEFILE_LIST)

login: ## Run GCloud and Pulumi logins
	pulumi logout
	pulumi login
	gcloud auth login
	gcloud auth application-default login

init: ## Create google project and pulumi stack
	gcloud projects create cdp-denver-962aefef --set-as-default
	pulumi stack init cdp-denver-962aefef

set-cors: ## Set cors for the bucket
	gsutil cors set cors.json gs://cdp-denver-962aefef.appspot.com/

destroy: ## Fully teardown Pulumi and GCloud
	pulumi stack rm cdp-denver-962aefef --force
	gcloud projects delete cdp-denver-962aefef
	rm -f .keys/cdp-denver-962aefef.json

gen-key: ## Generate a service account JSON
	mkdir .keys/ -p
	gcloud iam service-accounts create cdp-denver-962aefef \
    	--description="CDP Dev Service Account for $(USER)" \
    	--display-name="cdp-denver-962aefef"
	gcloud projects add-iam-policy-binding cdp-denver-962aefef \
    	--member="serviceAccount:cdp-denver-962aefef@cdp-denver-962aefef.iam.gserviceaccount.com" \
		--role="roles/owner"
	gcloud iam service-accounts keys create .keys/cdp-denver-962aefef.json \
  		--iam-account "cdp-denver-962aefef@cdp-denver-962aefef.iam.gserviceaccount.com"