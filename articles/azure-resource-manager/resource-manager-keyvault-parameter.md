---
title: Segreto di Key Vault con il modello di Azure Resource Manager | Microsoft Docs
description: Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 06719f3a92dae805081ea85c346df97ebed0e0dc
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078071"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile recuperare il valore da [Azure Key Vault](../key-vault/key-vault-whatis.md). Il valore viene recuperato facendo riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. L'insieme di credenziali delle chiavi può essere presente in una sottoscrizione diversa rispetto al gruppo di risorse in cui si sta eseguendo la distribuzione.

## <a name="deploy-a-key-vault-and-secret"></a>Distribuire un insieme di credenziali chiave e una chiave privata

Per creare un insieme di credenziali delle chiavi e un segreto, usare l'interfaccia della riga di comando di Azure o PowerShell. `enabledForTemplateDeployment` è una proprietà dell'insieme di credenziali delle chiavi. Per accedere ai segreti all'interno di Key Vault dalla distribuzione di Resource Manager, `enabledForTemplateDeployment` deve essere `true`. 

Lo script di esempio di Azure PowerShell e lo script di esempio dell'interfaccia della riga di comando di Azure illustrano come creare un Key Vault e un segreto.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Per PowerShell, usare:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Se l'esecuzione lo script di PowerShell è all'esterno di Cloud Shell, usare il comando seguente per generare la password:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Per l'uso del modello di Resource Manager, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Ogni servizio di Azure presenta requisiti specifici in termini di password. Ad esempio, i requisiti della macchina virtuale di Azure sono reperibili in [Quali requisiti devono avere le password durante la creazione di una macchina virtuale?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="enable-access-to-the-secret"></a>Abilitare l'accesso alla chiave privata

Oltre a impostare `enabledForTemplateDeployment` su `true`, l'utente che distribuisce il modello deve avere l'autorizzazione `Microsoft.KeyVault/vaults/deploy/action` per l'ambito che contiene Key Vault inclusi il gruppo di risorse e Key Vault. Entrambi i ruoli [Proprietario](../role-based-access-control/built-in-roles.md#owner) e [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) possono concedere l'accesso. Se si crea Key Vault si è i proprietari e pertanto si dispone dell'autorizzazione. Se Key Vault è in un'altra sottoscrizione, il proprietario dell'insieme di Key Vault deve fornire l'accesso.

La procedura seguente illustra come creare un ruolo con le autorizzazioni minime e come assegnarle all'utente
1. Creare un file JSON di definizione del ruolo personalizzato:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Sostituire "00000000-0000-0000-0000-000000000000" con l'ID sottoscrizione dell'utente che deve distribuire i modelli.

2. Creare il nuovo ruolo usando il file JSON:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    L'esempio `New-AzureRmRoleAssignment` assegna il ruolo personalizzato all'utente a livello di gruppo di risorse.  

Quando si usa un insieme di credenziali chiave con il modello per una [Applicazione gestita](../managed-applications/overview.md), è necessario concedere l'accesso all'entità servizio **Provider di risorse di Appliance**. Per altre informazioni, vedere [Segreto di accesso di Key Vault quando si distribuiscono Applicazioni gestite di Azure](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Fare riferimento a un segreto con un ID statico

Il modello che riceve un segreto dell'insieme di credenziali delle chiavi è come qualsiasi altro modello. Infatti **si fa riferimento all'insieme di credenziali delle chiavi nel file dei parametri, non nel modello**. L'immagine seguente mostra come il file dei parametri fa riferimento al segreto e passa tale valore al modello.

![ID statico](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Il [modello seguente](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json), ad esempio, distribuisce un database SQL che include una password dell'amministratore. Il parametro della password è impostato su una stringa sicura, ma il modello non specifica la provenienza di tale valore.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Creare ora un file dei parametri per il modello precedente. Nel file dei parametri specificare un parametro corrispondente al nome del parametro nel modello. Per il valore del parametro, fare riferimento al segreto dall'insieme di credenziali delle chiavi. Si fa riferimento alla chiave privata passando l'identificatore della risorsa dell'insieme di credenziali chiave e il nome della chiave privata. Nel [file dei parametri seguente](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json) il segreto dell'insieme di credenziali delle chiavi deve esistere già e si deve fornire un valore statico per il relativo ID risorsa. Copiare questo file in locale e impostare l'ID sottoscrizione, il nome dell'insieme di credenziali e il nome del server SQL.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Se è necessario usare una versione del segreto diversa da quella corrente, usare la proprietà `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

A questo punto, distribuire il modello e passare il file dei parametri. È possibile usare il modello di esempio da GitHub, ma è necessario usare un file di parametri locale con i valori impostati sull'ambiente in uso.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Per PowerShell, usare:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Fare riferimento a un segreto con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi dal parametro. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In alternativa, è possibile semplicemente passare i valori dei parametri al modello anziché creare un parametro di riferimento nel file dei parametri. In entrambi i casi, è possibile generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi usando un modello collegato.

Non è possibile generare in modo dinamico l'ID risorsa nel file dei parametri, perché le espressioni del modello non sono consentite nel file dei parametri. 

Nel modello padre aggiungere il modello collegato e passare un parametro che include l'ID risorsa generato in modo dinamico. La figura seguente mostra come un parametro nel modello collegato fa riferimento al segreto.

![ID dinamico](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Il [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) crea l'ID dell'insieme di credenziali delle chiavi in modo dinamico e lo passa come parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Distribuire il modello precedente e specificare i valori dei parametri. È possibile usare il modello di esempio da GitHub, ma è necessario specificare i valori dei parametri per l'ambiente in uso.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Per PowerShell, usare:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni generali sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).
* Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
