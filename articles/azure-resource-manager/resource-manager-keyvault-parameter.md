---
title: Segreto di Key Vault con il modello di Azure Resource Manager | Microsoft Docs
description: Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione.
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile recuperare il valore da [Azure Key Vault](../key-vault/key-vault-whatis.md). Il valore viene recuperato facendo riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. Non è necessario immettere manualmente il valore segreto ogni volta che si distribuisce le risorse. L'insieme di credenziali delle chiavi può essere presente in una sottoscrizione diversa rispetto al gruppo di risorse in cui si sta eseguendo la distribuzione. Quando si fa riferimento all'insieme di credenziali delle chiavi, includere l'ID sottoscrizione.

Quando di crea l'insieme di credenziali delle chiavi, impostare la proprietà *enabledForTemplateDeployment* su *true*. Impostando questo valore su true, si consente l'accesso dai modelli di Resource Manager durante la distribuzione.

## <a name="deploy-a-key-vault-and-secret"></a>Distribuire un insieme di credenziali chiave e una chiave privata

Per creare un insieme di credenziali delle chiavi e un segreto, usare l'interfaccia della riga di comando di Azure o PowerShell. Si noti che l'insieme di credenziali delle chiavi viene abilitato per la distribuzione dei modelli. 

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Per PowerShell, usare:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Abilitare l'accesso alla chiave privata

Indipendentemente dal fatto che si usi un insieme di credenziali delle chiavi nuovo o già esistente, assicurarsi che l'utente che distribuisce il modello possa accedere alla chiave privata. L'utente che distribuisce un modello che fa riferimento a una chiave privata deve disporre dell'autorizzazione `Microsoft.KeyVault/vaults/deploy/action` per l'insieme di credenziali delle chiavi. Entrambi i ruoli [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) e [Collaboratore](../active-directory/role-based-access-built-in-roles.md#contributor) possono concedere l'accesso.

## <a name="reference-a-secret-with-static-id"></a>Fare riferimento a un segreto con un ID statico

Il modello che riceve un segreto dell'insieme di credenziali delle chiavi è come qualsiasi altro modello. Infatti **si fa riferimento all'insieme di credenziali delle chiavi nel file dei parametri, non nel modello**. Il modello seguente, ad esempio, distribuisce un database SQL che include una password dell'amministratore. Il parametro della password è impostato su una stringa sicura, ma il modello non specifica la provenienza di tale valore.

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

Creare ora un file dei parametri per il modello precedente. Nel file dei parametri specificare un parametro corrispondente al nome del parametro nel modello. Per il valore del parametro, fare riferimento al segreto dall'insieme di credenziali delle chiavi. Si fa riferimento alla chiave privata passando l'identificatore della risorsa dell'insieme di credenziali chiave e il nome della chiave privata. Nell'esempio seguente il segreto dell'insieme di credenziali delle chiavi deve esistere già e si deve fornire un valore statico per il relativo ID risorsa.

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

A questo punto, distribuire il modello e passare il file dei parametri. Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Per PowerShell, usare:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Fare riferimento a un segreto con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In questo caso non è possibile impostare come hardcoded l'ID risorsa nel file dei parametri. Non è sfortunatamente possibile generare in modo dinamico l'ID risorsa nel file dei parametri, perché le espressioni del modello non sono consentite nel file dei parametri.

Per generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi, è necessario spostare la risorsa che necessita del segreto in un modello annidato. Nel modello principale aggiungere il modello annidato e passare un parametro che include l'ID risorsa generato in modo dinamico. Il modello annidato deve essere disponibile tramite un URI esterno. Nel resto di questo articolo si presuppone che il modello precedente sia stato aggiunto a un account di archiviazione e sia disponibile tramite l'URI - `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Distribuire il modello precedente e specificare i valori dei parametri.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni generali sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).
* Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
