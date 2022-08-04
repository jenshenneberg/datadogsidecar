# Datedog agent as a sidecar in Azure Container Apps
Example of running datadog agent as sidecar in [Azure Container Apps](https://azure.microsoft.com/en-us/services/container-apps/#overview)

## Expected behavior
We expect Azure Container Apps to work similar to Amazon Fargate, where the agent can be deployed as a sidecar. Description [here](https://www.datadoghq.com/blog/aws-fargate-monitoring-with-datadog/#deploy-the-agent-on-ecs)

However we face 2 problems.

1) The agent shows up fine in datadog, but it is charged as an APM host and not as a task (Fargate task in this [table](https://docs.datadoghq.com/account_management/billing/apm_tracing_profiler/#apm-hosts-fargate-and-indexed-spans-without-profiler) )
2) The datadog agent does not discover the other container running as it does on fargate.

Problem 1. is by far the biggest issue as it makes it far to expensive to use Datadog for monitoring Azure Container Apps.


## Create resources

Replace DD_API_KEY value below 
```bash
export DD_API_KEY="replace me"
az group create --location westus --resource-group datadogexample
az containerapp env create -n datadogexampleenv -g datadogexample --location westus
ENVIRONMENTID=$(az containerapp env show -g datadogexample -n datadogexampleenv --query id)
envsubst < datadogsidecartemplate.yaml > deploy.yaml 
az containerapp create --environment datadogexampleenv  --name datadogsidecarexample --resource-group datadogexample  --yaml deploy.yaml
```

## Clean up
```bash
az group delete --resource-group datadogexample
