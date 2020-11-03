---
title: Creare un insieme di credenziali delle chiavi di Azure e un criterio di accesso all'insieme di credenziali usando il modello ARM
description: Questo articolo illustra come creare insiemi di credenziali delle chiavi di Azure e criteri di accesso all'insieme di credenziali usando un modello di Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: d6f3d2051790ba02f5e4ea1687830ccbdf6dcf21
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285146"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Come creare un insieme di credenziali delle chiavi di Azure e i criteri di accesso all'insieme di credenziali usando un modello di Gestione risorse

[Azure Key Vault](../general/overview.md) è un servizio cloud che fornisce un archivio sicuro per segreti quali chiavi, password e certificati. Questo articolo descrive il processo di distribuzione di un modello di Azure Resource Manager (modello ARM) per creare un insieme di credenziali delle chiavi.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="create-a-key-vault-resource-manager-template"></a>Creare un modello di Gestione risorse Key Vault

Il modello seguente mostra un metodo di base per creare un insieme di credenziali delle chiavi. Alcuni valori vengono specificati nel modello.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Per altre informazioni sulle impostazioni del modello di Key Vault, vedere informazioni di [riferimento sui modelli ARM di Key Vault](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se viene ridistribuito un modello, eventuali criteri di accesso esistenti nell'insieme di credenziali delle chiavi verranno sostituiti. Si consiglia di popolare la `accessPolicies` proprietà con i criteri di accesso esistenti per evitare di perdere l'accesso all'insieme di credenziali delle chiavi. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Aggiungere un criterio di accesso a un modello di Gestione risorse Key Vault

È possibile distribuire i criteri di accesso a un insieme di credenziali delle chiavi esistente senza ridistribuire l'intero modello di Key Vault. Il modello seguente mostra un metodo di base per creare i criteri di accesso:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Per altre informazioni sulle impostazioni del modello di Key Vault, vedere informazioni di [riferimento sui modelli ARM di Key Vault](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Altri modelli di Key Vault Gestione risorse

Per gli oggetti Key Vault sono disponibili altri modelli di Gestione risorse:

| Segreti | Chiavi | Certificati |
|--|--|--|
|<ul><li>[Guida introduttiva](../secrets/quick-create-template.md)<li>[Riferimento](/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Qui è possibile trovare altri modelli di Key Vault: [Key Vault gestione risorse Reference](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Distribuire i modelli

È possibile usare il portale di Azure per distribuire i modelli precedenti usando l'opzione **Compila un modello personalizzato nell'editor** , come descritto qui: [distribuire le risorse da un modello personalizzato](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

È anche possibile salvare i modelli precedenti in file e usare questi comandi:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare le guide introduttive e le esercitazioni successive, è possibile lasciare invariate le risorse. Quando le risorse non sono più necessarie, eliminare il gruppo di risorse. Se si elimina il gruppo, verranno eliminati anche l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure o Azure PowerShell, completare i passaggi seguenti:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Risorse

- Leggi una [Panoramica del Azure Key Vault](../general/overview.md).
- Altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Esaminare [Azure Key Vault procedure consigliate](../general/best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)
- [Eseguire l'autenticazione in un insieme di credenziali delle chiavi](authentication.md)
- [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](developers-guide.md)
