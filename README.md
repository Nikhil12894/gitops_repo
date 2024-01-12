# gitops_repo
- A repo to store gitops manifest

## Install ArgoCD
### Step #1:Install ArgoCD on Minikube using Helm
once Minikube setup done, Add the ArgoCD Helm repository to Helm. Run the following command:
```sh
	git clone https://github.com/argoproj/argo-helm.git
```

- Change the folder where you need to install Argo using helm charts 
```sh
cd argo-helm/charts/argo-cd/
```
- Just like other Kubernetes tools, ArgoCD requires a namespace with its name. Therefore, we will create a namespace for argocd named myargo

```sh
kubectl create ns myargo
```
- You can modify any custom values in values.yaml. But for now we will process using default values.yaml

- Update the dependencies in the chart by executing the below command.
```sh
helm dependency up
```
- Install argo using helm command 

```sh
helm install myargo . -f values.yaml -n myargo

-- output: 
	NAME: myargo
	LAST DEPLOYED: Thu Jan 11 16:08:07 2024
	NAMESPACE: myargo
	STATUS: deployed
	REVISION: 1
	TEST SUITE: None
	NOTES:ß
	In order to access the server UI you have the following options:

	1. kubectl port-forward service/myargo-argocd-server -n myargo 80801:443

	    and then open the browser on http://localhost:80801 and accept the certificate

	2. enable ingress in the values file `server.ingress.enabled` and either
	      - Add the annotation for ssl passthrough: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-1-ssl-passthrough
	      - Set the `configs.params."server.insecure"` in the values file and terminate SSL at your ingress: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-multiple-ingress-objects-and-hosts


	After reaching the UI the first time you can login with username: admin and the random password generated during the installation. You can find the password by running:

	kubectl -n myargo get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

	(You should delete the initial secret afterwards as suggested by the Getting Started Guide: https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)
```

- Error:
```
Error: rendered manifests contain a resource that already exists. Unable to continue with install: CustomResourceDefinition "applications.argoproj.io" in namespace "" exists and cannot be imported into the current release: invalid ownership metadata; label validation error: missing key "app.kubernetes.io/managed-by": must be set to "Helm"; annotation validation error: missing key "meta.helm.sh/release-name": must be set to "myargo"; annotation validation error: missing key "meta.helm.sh/release-namespace": must be set to "myargo"
```
- Solution:

If you face the above issues with CRDs, then comment crds install value to ‘false’ in values.yaml. Refer to the below command.  
```yml
crds:
# -- Install and upgrade CRDs
install: false
```