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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 93b92a8a3b8aacd1f665725643314858fe92ad3c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233769"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione

Invece di inserire un valore protetto (ad esempio una password) direttamente nel file dei parametri, è possibile recuperare il valore da [Azure Key Vault](../key-vault/key-vault-whatis.md) durante una distribuzione. Il valore viene recuperato facendo riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. L'insieme di credenziali delle chiavi può essere presente in una sottoscrizione diversa rispetto al gruppo di risorse in cui si sta eseguendo la distribuzione.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>Distribuire insiemi di credenziali delle chiavi e segreti

Per creare Key Vault e aggiungere i segreti, vedere:

- [Impostare e recuperare un segreto usando l'interfaccia della riga di comando](../key-vault/quick-create-cli.md)
- [Impostare e recuperare un segreto usando Powershell](../key-vault/quick-create-powershell.md)
- [Impostare e recuperare un segreto usando il portale](../key-vault/quick-create-portal.md)
- [Impostare e recuperare un segreto usando .NET](../key-vault/quick-create-net.md)
- [Impostare e recuperare un segreto usando Node.js](../key-vault/quick-create-node.md)

Esistono altri requisiti e considerazioni quando si esegue l'integrazione di Key Vault con la distribuzione dei modelli di Resource Manager:

- `enabledForTemplateDeployment` è una proprietà dell'insieme di credenziali delle chiavi. Per accedere ai segreti all'interno di Key Vault dalla distribuzione di Resource Manager, `enabledForTemplateDeployment` deve essere `true`. 
- Se l'utente non è il proprietario dell'insieme di credenziali delle chiavi, il proprietario deve aggiornare le impostazioni dei criteri di sicurezza dell'insieme di credenziali delle chiavi perché sia possibile aggiungere i segreti.

Gli esempi dell'interfaccia della riga di comando di Azure e di Azure PowerShell seguenti mostrano come farlo:

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>Concedere l'accesso ai segreti

L'utente che distribuisce il modello deve avere l'autorizzazione `Microsoft.KeyVault/vaults/deploy/action` per l'ambito che contiene Key Vault inclusi il gruppo di risorse e Key Vault. Entrambi i ruoli [Proprietario](../role-based-access-control/built-in-roles.md#owner) e [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) possono concedere l'accesso. Se si crea Key Vault si è i proprietari e pertanto si dispone dell'autorizzazione. Se Key Vault è in un'altra sottoscrizione, il proprietario dell'insieme di Key Vault deve fornire l'accesso.

La procedura seguente illustra come creare un ruolo con le autorizzazioni minime e come assegnarlo all'utente

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
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    L'esempio `New-AzRoleAssignment` assegna il ruolo personalizzato all'utente a livello di gruppo di risorse.  

Quando si usa un insieme di credenziali chiave con il modello per una [Applicazione gestita](../managed-applications/overview.md), è necessario concedere l'accesso all'entità servizio **Provider di risorse di Appliance**. Per altre informazioni, vedere [Segreto di accesso di Key Vault quando si distribuiscono Applicazioni gestite di Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Fare riferimento a segreti con un ID statico

Con questo approccio, si fa riferimento all'insieme di credenziali delle chiavi nel file dei parametri, non nel modello. L'immagine seguente mostra come il file dei parametri fa riferimento al segreto e passa tale valore al modello.

![Diagramma di un ID statico per l'integrazione dell'insieme di credenziali delle chiavi di Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md) usando questo metodo. L'esercitazione distribuisce una macchina virtuale che include una password dell'amministratore. Il parametro della password è impostato su una stringa sicura:

![File modello di un ID statico per l'integrazione dell'insieme di credenziali delle chiavi di Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

Creare ora un file dei parametri per il modello precedente. Nel file dei parametri specificare un parametro corrispondente al nome del parametro nel modello. Per il valore del parametro, fare riferimento al segreto dall'insieme di credenziali delle chiavi. Si fa riferimento al segreto passando l'identificatore della risorsa dell'insieme di credenziali delle chiavi e il nome del segreto:

![File dei parametri di un ID statico per l'integrazione dell'insieme di credenziali delle chiavi di Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Se è necessario usare una versione del segreto diversa da quella corrente, usare la proprietà `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuire il modello e passare il file dei parametri:

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Per PowerShell, usare:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Fare riferimento a segreti con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi dal parametro. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In alternativa, è possibile passare i valori dei parametri al modello anziché creare un parametro di riferimento nel file dei parametri. In entrambi i casi, è possibile generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi usando un modello collegato.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Per PowerShell, usare:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](../key-vault/key-vault-overview.md).
- Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
