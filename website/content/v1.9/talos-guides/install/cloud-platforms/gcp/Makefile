credentials:
	OUTPUTS=$$(gcloud deployment-manager deployments describe "${DEPLOYMENT_NAME}" --format json | jq '.outputs[]'); \
	BUCKET_NAME=$$(jq -r '. | select(.name == "bucketName").finalValue' <<< "$${OUTPUTS}"); \
	SERVICE_ACCOUNT=$$(jq -r '. | select(.name == "serviceAccount").finalValue' <<< "$${OUTPUTS}"); \
	PROJECT=$$(jq -r '. | select(.name == "project").finalValue' <<< "$${OUTPUTS}"); \
	gsutil cp "gs://$${BUCKET_NAME}/generated/talosconfig" . ;\
	gsutil cp "gs://$${BUCKET_NAME}/generated/kubeconfig" .

create:
	if [[ ! -f config.yaml ]]; then cp config-template.yaml config.yaml; fi
	gcloud deployment-manager deployments create "${DEPLOYMENT_NAME}" --config config.yaml

update:
	gcloud deployment-manager deployments update "${DEPLOYMENT_NAME}" --config config.yaml

delete:
	OUTPUTS=$$(gcloud deployment-manager deployments describe "${DEPLOYMENT_NAME}" --format json | jq '.outputs[]') && \
	BUCKET_NAME=$$(jq -r '. | select(.name == "bucketName").finalValue' <<< "$${OUTPUTS}") && \
	SERVICE_ACCOUNT=$$(jq -r '. | select(.name == "serviceAccount").finalValue' <<< "$${OUTPUTS}") && \
	PROJECT=$$(jq -r '. | select(.name == "project").finalValue' <<< "$${OUTPUTS}") &&  \
	if [ -z "$${BUCKET_NAME}" ]; then gcloud storage rm -r "gs://$${BUCKET_NAME}"; fi; \
	time yes | gcloud deployment-manager deployments delete "${DEPLOYMENT_NAME}" --delete-policy=delete
