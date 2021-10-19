# Download artifact from latest build in pipleine
```
$buildId = az  pipelines build list --org https://dev.azure.com/company-name/ --project project-name --definition-ids 'id if pipeline' --status completed --query "[?contains(buildNumber, 'Name of the build')].[id]|[0]" --output tsv
az pipelines runs artifact download --org https://dev.azure.com/company-name/ --project project-name --run-id $buildId --artifact-name deploy-output --path ./
```


# Copy to azure blob storage from script
```
- task: AzureCLI@2
displayName: List
inputs:
  azureSubscription: 'DEV'
  scriptType: ps
  scriptLocation: inlineScript
  inlineScript: |
        Write-Host $(System.DefaultWorkingDirectory)/web/DEV
        az storage blob upload-batch --destination web-app --account-name my-account-name  --destination-path test --source $(System.DefaultWorkingDirectory)/web/dist
```

# Generate Azure storage share SAS code
```
    - task: AzureCLI@2
      displayName: List
      inputs:
        azureSubscription: 'DEV'
        scriptType: ps
        scriptLocation: inlineScript
        inlineScript: |
              $accountName='storage account name'
              az storage account keys list --account-name $accountName -g APP-WW-ENV-DEV --query "[?contains(keyName, 'key1')].[value]" --output tsv
              $accountKey=$(az storage account keys list --account-name $accountName -g APP-TR-11256-DEV --query "[?contains(keyName, 'key1')].[value]" --output tsv)
              $end=$((get-date).ToUniversalTime().AddMinutes(10).ToString("yyyy-MM-ddTHH:mm:00Z"))
              $accountName
              $accountKey
              $end
              $sas=$(az storage share generate-sas -n share-name --account-key $accountKey --account-name $accountName --https-only --permissions lr --expiry $end -o tsv)
              $sas
```

# Resources
[Query results, jmespath](https://www.azurecitadel.com/cli/jmespath/)
[How to query azure resources](https://techcommunity.microsoft.com/t5/itops-talk-blog/how-to-query-azure-resources-using-the-azure-cli/ba-p/360147)


