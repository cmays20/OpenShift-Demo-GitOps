# ACS ArgoCD Gitops Install

## Health Check

The following is needed to be added to the openshift-gitops ArgoCD Details object

```yaml
spec:
  #SNIPPET
  resourceHealthChecks:
    - check: |
        hs = {}
        if obj.status ~= nil and obj.status.conditions ~= nil then
          for i, condition in ipairs(obj.status.conditions) do
            if condition.status == "True" and (condition.reason == "InstallSuccessful" or condition.reason =="UpgradeSuccessful") then
              hs.status = "Healthy"
              hs.message = condition.message
              return hs
            end
          end
        end
        hs.status = "Progressing"
        hs.message = "Waiting for Central to deploy."
        return hs  
      group: platform.stackrox.io
      kind: Central
```