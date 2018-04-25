# Files
There are a number of files contained within this repo that are worth noting:

    1. build.json - a fully realized build document representing an actual customer
    2. azuredeploy.parameters.json - the parameters file for a deployment that contains a single environment from the build.json
    3. azuredeploy.json - a template file that does not build anything, but outputs templates
    4. output.txt - the output of a test run, to show you what it looks like

# Build and Test
This can very easily be run, and most likely should be run, from within PowerShell.
```
New-AzureRmResourceGroupDeployment -Name testing -ResourceGroupName testing-rg -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Verbose
```
The resulting templates are not fully complete, but they are in fact, quite close, and with very little effort could be deployed into Azure. One item worth noting, the resulting templates are targeted to a customers specific environment, meaning that even when run against your subscription, the output will *only* work in the customer's subscription. 
```
"availabilitySet": {
  "id": "/subscriptions/f2567e29-dd77-4151-9b7b-4127e1f902c7/resourceGroups/eus-ad/providers/Microsoft.Compute/availabilitySets/x-win-ad-avset"
},
```
The id above is built from the resource below
```
"availabilitySet": {
  "id": "[resourceId(parameters('build').subscriptionId,parameters('build').virtualMachines[copyIndex('vmLoop')].ResourceGroup,'Microsoft.Compute/availabilitySets',parameters('build').virtualMachines[copyIndex('vmLoop')].availabilitySet)]"
},
```

# odds and ends
There are some files that I'll list here that are interesting

    1. nestedcopy.json - Working on the assumption a given environment could contain n+1 vnets', nested copy would be ideal, the template to generate this is simple yet invalid.
    2. weirdnsgid.json - The build.json contains the subscriptionId, and this is stored inside a variable object 'environment', but in the nsgId section of the subnet, referencing that as variables('environment').subscriptionId, yields the literal text from the variable declaration, parameters('build').subscriptionId
    3. deploy.json - This is a playground file
