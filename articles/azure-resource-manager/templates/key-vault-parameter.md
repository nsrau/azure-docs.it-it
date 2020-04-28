---
title: Key Vault segreto con il modello
description: Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458267"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione

Anziché inserire un valore protetto (ad esempio una password) direttamente nel modello o nel file di parametri, è possibile recuperare il valore da un [Azure Key Vault](../../key-vault/general/overview.md) durante una distribuzione. Il valore viene recuperato facendo riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. L'insieme di credenziali delle chiavi può esistere in una sottoscrizione diversa da quella del gruppo di risorse in cui si esegue la distribuzione.

Questo articolo è incentrato sullo scenario di passaggio di un valore sensibile in come parametro di modello. Non copre lo scenario di impostazione di una proprietà della macchina virtuale sull'URL di un certificato in un Key Vault. Per un modello di avvio rapido di tale scenario, vedere [installare un certificato da Azure Key Vault in una macchina virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Distribuire insiemi di credenziali delle chiavi e segreti

Per accedere a un insieme di credenziali delle chiavi durante `enabledForTemplateDeployment` la distribuzione del modello, `true`impostare nell'insieme di credenziali delle chiavi su.

Se si dispone già di un Key Vault, assicurarsi che consenta le distribuzioni del modello.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Per creare una nuova Key Vault e aggiungere un segreto, usare:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Il proprietario dell'insieme di credenziali delle chiavi consente di accedere automaticamente alla creazione di segreti. Se l'utente che lavora con i segreti non è il proprietario dell'insieme di credenziali delle chiavi, concedere l'accesso con:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Per ulteriori informazioni sulla creazione di insiemi di credenziali delle chiavi e sull'aggiunta di segreti, vedere:

- [Impostare e recuperare un segreto usando l'interfaccia della riga di comando](../../key-vault/secrets/quick-create-cli.md)
- [Impostare e recuperare un segreto usando Powershell](../../key-vault/secrets/quick-create-powershell.md)
- [Impostare e recuperare un segreto usando il portale](../../key-vault/secrets/quick-create-portal.md)
- [Impostare e recuperare un segreto usando .NET](../../key-vault/secrets/quick-create-net.md)
- [Impostare e recuperare un segreto usando Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Concedere l'accesso ai segreti

L'utente che distribuisce il modello deve avere l' `Microsoft.KeyVault/vaults/deploy/action` autorizzazione per l'ambito del gruppo di risorse e di Key Vault. Entrambi i ruoli [Proprietario](../../role-based-access-control/built-in-roles.md#owner) e [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) possono concedere l'accesso. Se è stato creato l'insieme di credenziali delle chiavi, si è il proprietario, quindi si dispone dell'autorizzazione.

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
    Sostituire "00000000-0000-0000-0000-000000000000" con l'ID sottoscrizione.

2. Creare il nuovo ruolo usando il file JSON:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Gli esempi assegnano il ruolo personalizzato all'utente a livello di gruppo di risorse.

Quando si usa un insieme di credenziali chiave con il modello per una [Applicazione gestita](../managed-applications/overview.md), è necessario concedere l'accesso all'entità servizio **Provider di risorse di Appliance**. Per altre informazioni, vedere [Segreto di accesso di Key Vault quando si distribuiscono Applicazioni gestite di Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Fare riferimento a segreti con un ID statico

Con questo approccio, si fa riferimento all'insieme di credenziali delle chiavi nel file dei parametri, non nel modello. L'immagine seguente mostra come il file dei parametri fa riferimento al segreto e passa tale valore al modello.

![Diagramma di un ID statico per l'integrazione dell'insieme di credenziali delle chiavi di Resource Manager](./media/key-vault-parameter/statickeyvault.png)

[Esercitazione: integrare Azure Key Vault in Gestione risorse distribuzione modelli](./template-tutorial-use-key-vault.md) usa questo metodo.

Il modello seguente distribuisce un server SQL che include una password di amministratore. Il parametro della password è impostato su una stringa sicura, Tuttavia, il modello non specifica da dove deriva tale valore.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Creare ora un file dei parametri per il modello precedente. Nel file dei parametri specificare un parametro corrispondente al nome del parametro nel modello. Per il valore del parametro, fare riferimento al segreto dall'insieme di credenziali delle chiavi. Si fa riferimento al segreto passando l'identificatore della risorsa dell'insieme di credenziali delle chiavi e il nome del segreto:

Nel file dei parametri seguente il segreto dell'insieme di credenziali delle chiavi deve esistere già e si deve fornire un valore statico per il relativo ID risorsa.

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
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuire il modello e passare il file dei parametri:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Fare riferimento a segreti con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi dal parametro. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In alternativa, è possibile passare i valori dei parametri al modello anziché creare un parametro di riferimento nel file dei parametri. In entrambi i casi, è possibile generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi usando un modello collegato.

Non è possibile generare in modo dinamico l'ID risorsa nel file dei parametri, perché le espressioni del modello non sono consentite nel file dei parametri.

Nel modello padre aggiungere il modello annidato e passare un parametro che contiene l'ID risorsa generato in modo dinamico. La figura seguente mostra come un parametro nel modello collegato fa riferimento al segreto.

![ID dinamico](./media/key-vault-parameter/dynamickeyvault.png)

Il modello seguente crea l'ID dell'insieme di credenziali delle chiavi in modo dinamico e lo passa come parametro.

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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](../../key-vault/general/overview.md).
- Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
