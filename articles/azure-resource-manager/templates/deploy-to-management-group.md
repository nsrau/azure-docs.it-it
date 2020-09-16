---
title: Distribuire le risorse al gruppo di gestione
description: Viene descritto come distribuire le risorse nell'ambito del gruppo di gestione in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 2325e9f5a03f7451492c9b9b8e929df95ddc3852
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605227"
---
# <a name="create-resources-at-the-management-group-level"></a>Creazione di risorse a livello di gruppo di gestione

Man mano che l'organizzazione è matura, è possibile distribuire un modello di Azure Resource Manager (ARM template) per creare risorse a livello di gruppo di gestione. Ad esempio, potrebbe essere necessario definire e assegnare [criteri](../../governance/policy/overview.md) o il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) per un gruppo di gestione. Con i modelli a livello di gruppo di gestione è possibile applicare i criteri in modo dichiarativo e assegnare i ruoli a livello di gruppo di gestione.

## <a name="supported-resources"></a>Risorse supportate

Non tutti i tipi di risorse possono essere distribuiti al livello del gruppo di gestione. Questa sezione elenca i tipi di risorse supportati.

Per i progetti di Azure, usare:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versioni](/azure/templates/microsoft.blueprint/blueprints/versions)

Per i criteri di Azure, usare:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Per il controllo degli accessi in base al ruolo, usare:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Per i modelli annidati che vengono distribuiti nelle sottoscrizioni o nei gruppi di risorse, usare:

* [distribuzioni](/azure/templates/microsoft.resources/deployments)

Per la gestione delle risorse, usare:

* [tag](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni di gruppi di gestione è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni di gruppi di gestione sono diversi da quelli per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure, usare [AZ Deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Per Azure PowerShell, usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Per l'API REST, usare [distribuzioni-crea nell'ambito del gruppo di gestione](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di gruppo di gestione, è necessario specificare un percorso per la distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un gruppo di gestione, è possibile usare come destinazione il gruppo di gestione specificato nel comando di distribuzione o altri gruppi di gestione nel tenant. È anche possibile fare riferimento a sottoscrizioni o gruppi di risorse all'interno di un gruppo di gestione. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Le risorse definite nella sezione Resources del modello vengono applicate al gruppo di gestione dal comando Deployment.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

Per fare riferimento a un altro gruppo di gestione, aggiungere una distribuzione annidata e specificare la `scope` Proprietà.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template-with-resources-in-different-mg
                }
            }
        }
    ],
    "outputs": {}
}
```

Per fare riferimento a una sottoscrizione all'interno del gruppo di gestione, usare una distribuzione annidata e la `subscriptionId` Proprietà. Per fare riferimento a un gruppo di risorse all'interno di tale sottoscrizione, aggiungere un'altra distribuzione annidata e la `resourceGroup` Proprietà.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template-with-resources-in-resource-group
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

Per usare una distribuzione del gruppo di gestione per creare un gruppo di risorse all'interno di una sottoscrizione e distribuire un account di archiviazione a tale gruppo di risorse, vedere [distribuire a una sottoscrizione e un gruppo di risorse](#deploy-to-subscription-and-resource-group).

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni di gruppi di gestione, è necessario tenere presenti alcune considerazioni importanti relative all'utilizzo delle funzioni modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [subscription()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Non usare la funzione [ResourceID ()](template-functions-resource.md#resourceid) per le risorse distribuite nel gruppo di gestione.

  Usare invece la funzione [extensionResourceId ()](template-functions-resource.md#extensionresourceid) per le risorse implementate come estensioni del gruppo di gestione. Le definizioni dei criteri personalizzati che vengono distribuite nel gruppo di gestione sono estensioni del gruppo di gestione.

  Per ottenere l'ID risorsa per una definizione di criteri personalizzata a livello di gruppo di gestione, usare:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Usare la funzione [tenantResourceId](template-functions-resource.md#tenantresourceid) per le risorse tenant disponibili all'interno del gruppo di gestione. Le definizioni dei criteri predefinite sono risorse a livello di tenant.

  Per ottenere l'ID risorsa per una definizione dei criteri predefinita, usare:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Criteri di Azure

Nell'esempio seguente viene illustrato come [definire](../../governance/policy/concepts/definition-structure.md) un criterio a livello di gruppo di gestione e assegnarlo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuisci nella sottoscrizione e nel gruppo di risorse

Da una distribuzione a livello di gruppo di gestione, è possibile scegliere come destinazione una sottoscrizione all'interno del gruppo di gestione. L'esempio seguente crea un gruppo di risorse all'interno di una sottoscrizione e distribuisce un account di archiviazione a tale gruppo di risorse.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione dei ruoli, vedere [aggiungere assegnazioni di ruolo di Azure usando modelli di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* È anche possibile distribuire modelli a livello di [sottoscrizione](deploy-to-subscription.md) e [tenant](deploy-to-tenant.md).
