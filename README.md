# Helm Links
[argo-cd](https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd)

[aws-ebs-csi-driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/tree/master/charts/aws-ebs-csi-driver)

[aws-load-balancer-controller](https://github.com/aws/eks-charts/tree/master/stable/aws-load-balancer-controller)

[crossplane](https://charts.crossplane.io/stable)

[csi-secrets-store-provider-aws](https://github.com/aws/eks-charts/tree/master/stable/csi-secrets-store-provider-aws)

* [secrets-store-csi-driver](https://github.com/kubernetes-sigs/secrets-store-csi-driver/tree/main/charts/secrets-store-csi-driver)

[ingress-nginx](https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx)

[loki-stack](https://github.com/grafana/helm-charts/tree/main/charts/loki-stack)

* [loki](https://github.com/grafana/helm-charts/tree/main/charts/loki)

* [promtail](https://github.com/grafana/helm-charts/tree/main/charts/promtail)

* [fluent-bit](https://github.com/fluent/helm-charts/tree/main/charts/fluent-bit)

* [grafana](https://github.com/grafana/helm-charts/tree/main/charts/grafana)

* [prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus)

  * [kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics)

* [filebeat](https://github.com/elastic/helm-charts/tree/main/filebeat)

* [logstash](https://github.com/elastic/helm-charts/tree/main/logstash)

[mimir-distributed](https://github.com/grafana/helm-charts/tree/main/charts/mimir-distributed)

* [minio](https://github.com/minio/minio/tree/master/helm/minio)

* [grafana-agent-operator](https://github.com/grafana/helm-charts/tree/main/charts/agent-operator)

# Document

#### 1. Update the on-disk dependencies to mirror `Chart.yaml`
```console
helm dependency update <chartName>
```
Above command verifies that the required charts expressed in `Chart.yaml` are present in `chartName/charts/` and are at an acceptable version. It will pull down the latest charts that satisfy the dependencies and clean up old dependencies. On successful update, this will generate a lock file that can be used to rebuild the dependencies to an exact version.
#### 2. `helm lint` runs a series of tests to verify that the chart is well-formed
```console
helm lint <chartName>
```
#### 3. Run this command if you get `[WARNING] <chartName>: chart directory is missing these dependencies: <dependencyChartName>` in response to the previous command
```console
tar -xvzf <chartName/charts/dependencyChartName.tgz>
```
#### 4. `package` the Helm chart
```console
helm package <chartName>
```
#### 5. Create an ECR repository to store Helm chart. The name of your repository should match the name of the Helm chart
```console
aws ecr create-repository --repository-name <chartName> --image-scanning-configuration scanOnPush=true --region <your-region> --profile <your-aws-profile-name>
```
#### 6. Authenticate Helm client to the Amazon ECR registry
```console
aws ecr get-login-password --region <your-region> --profile <your-aws-profile-name> | helm registry login --username AWS --password-stdin <YOUR-AWS-ACCOUNT-ID>.dkr.ecr.<your-region>.amazonaws.com
```
#### 7. Push Helm chart
```console
helm push <chartName-chartVersion>.tgz oci://<YOUR-AWS-ACCOUNT-ID>.dkr.ecr.<your-region>.amazonaws.com
```
#### 8. To describe Helm chart
```console
aws ecr describe-images --repository-name <chartName> --region <your-region> --profile <your-aws-profile-name>
```
#### 9. To install the Helm chart
```console
helm install <applicationName> --create-namespace --namespace <k8s-namespace> oci://<YOUR-AWS-ACCOUNT-ID>.dkr.ecr.<your-region>.amazonaws.com/<chartName> --version <chartVersion>
```
#### 10. To uninstall the Helm chart
```console
helm delete <applicationName> --namespace <k8s-namespace>
```
