# falcon-mirror-repository

Simple github workflow to sync the following Crowdstrike Cloud Workload Protection container images to your own docker repository using skopeo sync.
- Falcon Container Sensor
- Falcon Sensor for Linux
- Kubernetes Protection Agent

Images are synced daily, existing images are skipped.

# Quickstart

Create destination repositories in your container image registry
- `falcon-sensor`
- `kpagent`

Fork this repository.

Create the following [Repository Secrets](https://docs.github.com/en/codespaces/managing-codespaces-for-your-organization/managing-encrypted-secrets-for-your-repository-and-organization-for-github-codespaces#adding-secrets-for-a-repository)

Enable workflows in the repository `Actions` tab

| Secret  | Description	| Example  	|
|---	|--- |---	|
| FALCON_CID  	| Copy from the sensor download page  | SOMETHING4DI30382C85D952A0A682E-AE  	|
| FALCON_CLIENT_ID  | Falcon platform API key with the following scopes: Falcon Images Download (read), Kubernetes Protection (read)	| somethingsomething05ec5aaada734d  	|
| FALCON_CLIENT_SECRET | Secrete generated with Client ID 	| somethingsomethingh9qFwKB8ij02fQbMRsdr  	|
| FALCON_CLOUD_API  | API endpoint for your cloud ( us-1 / us-2 / eu-1 / gov-1 ) | (api.crowdstrike.com / api.us-2crowdstrike.com / api.eu-1.crowdstrike.com / api.laggar.gcw.crowdstrike.com )  	|
| FALCON_CONTAINER_REGISTRY | Your registry endpoint ( us-1 / us-2 / eu-1 / gov-1 )   	| (registry.crowdstrike.com / registry.crowdstrike.com / registry.crowdstrike.com / registry.laggar.gcw.crowdstrike.com )  	|
| YOUR_REGISTRY  	| your destination docker registry | 123456789012.dkr.ecr.ap-southeast-1.amazonaws.com  	|
| YOUR_REGISTRY_USERNAME  	| your destination docker registry username | AWS  	|
| YOUR_REGISTRY_PASSWORD  	| your destination docker registry password | yourdockerregistrypassword  	|

# Next Steps

Use the following to deploy the images in your environment:

## Falcon Sensor (as a DaemonSet or Sidecar)

- [CrowdStrike Falcon Helm Chart](https://github.com/CrowdStrike/falcon-helm)
- [Operator](https://github.com/CrowdStrike/falcon-operator)

## Kubernetes Protection Agent
You can get the instructions and `dockerAPIToken` from the `Cloud Accounts Registration -> Kubernetes -> Self-Managed Kubernetes Service` page.
```
helm repo add kpagent-helm https://registry.crowdstrike.com/kpagent-helm
helm upgrade kpagent kpagent-helm/cs-k8s-protection-agent \
    --install -n falcon-kubernetes-protection 
    --create-namespace
    --set crowdstrikeConfig.clusterName="clustername"
    --set crowdstrikeConfig.enableDebug="false"
    --set crowdstrikeConfig.clientID="somethingsomethingh9qFwKB8ij02fQbMRsdr"
    --set crowdstrikeConfig.env="us-1"
    --set crowdstrikeConfig.cid="something4di30382c85d952a0a682e"
    --set crowdstrikeConfig.dockerAPIToken="AKCp8nG6NJsz9UHHPYZ4RLbguaVNF2ghJvruJPnkSN6tSACxLnea1YfHbwks4t39yjPy4VEan"
```