# Flux setup and bootstrap

   flux check --pre

  export GITHUB_USER=bengreen

   flux bootstrap github \
    --owner=polecatworks \
    --repository=home-k8s \
    --branch=main \
    --path=./clusters/home-k8s


# Ensure your have provided secrets for the helm repos you want to reference. Eg in flux-system

    flux create secret helm helm-ghcr-io -u <USERNAME> -p ${GITHUB_TOKEN}

# You may also wish to create docker pull secrets

Reference from here https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

Or via a single command line

    kubectl create secret docker-registry polecatworks-clasic --docker-server=ghcr.io --docker-username=<USERNAME> --docker-password=${GHCR_TOKEN}

    kubectl create secret generic bproxy0-bucket-proxy --from-literal=access_key_id=<KEYID> --from-literal=secret_key=<SECRET>


# Sort out PG Poolusers

PG_PASSWD_KEYCLOAK=$(kubectl -n security get secrets keycloak-postgresql -o jsonpath="{.data.DB_PASS}" | base64 -d)
PG_PASSWD_LOG4HAM=$(kubectl -n log4ham get secrets log4ham-pg -o jsonpath="{.data.DB_PASS}" | base64 -d)
PG_PASSWD_DEV=$(kubectl -n dev get secrets example-postgres-db-credentials -o jsonpath="{.data.DB_PASS}" | base64 -d)
