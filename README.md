# Cheapskate

A simple scripted tool that cleans up resources hosted in Azure. Can be used to save money by periodically destroying test or sandbox environments.

This tool leverages the Azure CLI to automatically destroy [Deployment Stacks](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deployment-stacks?tabs=azure-powershell).

 - This tool is intended to be paired with some flavor of infrastructure-as-code like Bicep or Terraform. I would not use this tool when provisioning resources via ClickOps.

 - This tool is intended to be run as part of a pipeline, not from a personal machine. From the pipeline, you may setup a schedule to the tool or run it manually as needed.

## Motivation

Many Azure resources require infrastructure provisioning or come with comptue costs even if these services are not being actively used.

## Usage

Simply execute `cheapskate.ps1` in a pipeline that has been authenticated with Azure. 

This will destroy all deployment stacks within the subscription scope** of the authenticated Azure identity.

** WARNING: Accidentally executing this tool against a production environment could be a career-limiting move for you. Be very diligent to ensure the identity you use is authenticated against a non-production subscription scope.

```
- name: Delete stacks
  shell: pwsh
  run: ${{ github.workspace }}/cheapskate.ps1
```

![image](https://github.com/jamesSampica/cheapskate/assets/2416676/7639cf77-f124-4188-8ea8-d3a09b445554)


## Excluding Stacks

Set the `EXCLUDE_STACKS` environment variable to a JSON array containing the names of the deployment stacks you want to exclude. These stacks will be skipped when the script executes.

```
- name: Delete stacks
  shell: pwsh
  run: ${{ github.workspace }}/cheapskate.ps1
  env:
    EXCLUDE_STACKS: |
      [ 'stack1', 'stack2' ]
```

![image](https://github.com/jamesSampica/cheapskate/assets/2416676/2cd46f02-ab60-4125-893c-918a5fc1ef95)


## Limitations

This tool only destroys deployment stacks at the resource group level. Destroying subscription level deployment stacks is not supported.

