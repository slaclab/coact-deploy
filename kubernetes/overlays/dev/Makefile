MONGO_OPERATOR_VERSION ?= 1.11.0

SECRET_PATH ?= secret/tid/coact/mongo

# hmm... upgrades manual?? https://www.percona.com/doc/kubernetes-operator-for-psmongodb/update.html
update-mongo-operator: 
	helm repo add percona https://percona.github.io/percona-helm-charts/
	helm repo update
	curl -L https://raw.githubusercontent.com/percona/percona-server-mongodb-operator/v$(MONGO_OPERATOR_VERSION)/deploy/crd.yaml > mongo-operator-crds.yaml
	curl -L https://raw.githubusercontent.com/percona/percona-server-mongodb-operator/v$(MONGO_OPERATOR_VERSION)/deploy/rbac.yaml > mongo-operator-rbac.yaml 
	helm template --version $(MONGO_OPERATOR_VERSION) mongodb-operator percona/psmdb-operator > mongo-operator.yaml

secrets:
	mkdir -p etc/.secrets/
	for i in MONGODB_BACKUP_PASSWORD MONGODB_CLUSTER_ADMIN_PASSWORD MONGODB_CLUSTER_MONITOR_PASSWORD MONGODB_USER_ADMIN_PASSWORD PMM_SERVER_PASSWORD S3_ACCESS_KEY S3_SECRET_KEY COACT_USER COACT_PASSWORD; do vault kv get -format=table  --field=$$i $(SECRET_PATH) > etc/.secrets/$$i; done

clear-secrets:
	rm -rf etc/.secrets/

run-apply:
	kubectl apply -k .

dump:
	kubectl kustomize .

apply: secrets run-apply clear-secrets
