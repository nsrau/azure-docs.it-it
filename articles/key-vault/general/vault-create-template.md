---
title: Guida di Azure-creare un insieme di credenziali delle chiavi di Azure e un criterio di accesso all'insieme di credenziali usando Azure Resource Manager modello | Microsoft Docs
description: Illustra come creare insiemi di credenziali delle chiavi di Azure e criteri di accesso all'insieme di credenziali usando Azure Resource Manager modello.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804396"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Come creare criteri di accesso a Azure Key Vault e insieme di credenziali usando un modello di Gestione risorse

[Azure Key Vault](../general/overview.md) è un servizio cloud che offre un archivio sicuro per i segreti, ad esempio le chiavi, le password, i certificati e così via. Questa guida è incentrata sul processo di distribuzione di un modello di Azure Resource Manager (modello ARM) per creare un insieme di credenziali delle chiavi.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="create-key-vault-resource-manager-template"></a>Creare Key Vault modello di Gestione risorse

Il modello seguente mostra un metodo di base per creare Key Vault. Alcuni valori sono specificati all'interno del modello.

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

Per altre informazioni sulle impostazioni del modello di Key Vault, vedere informazioni di [riferimento sui modelli ARM di Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se il modello viene ridistribuito, eseguirà l'override dei criteri di accesso esistenti nell'insieme di credenziali delle chiavi. È consigliabile popolare `accessPolicies` la proprietà con i criteri di accesso esistenti per evitare di perdere l'accesso a Key Vault. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Aggiungere i criteri di accesso al modello di Gestione risorse Key Vault

È possibile distribuire i criteri di accesso all'insieme di credenziali delle chiavi esistente senza ridistribuire l'intero modello di Key Vault. Il modello seguente mostra un metodo di base per creare criteri di accesso.

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
Per altre informazioni sulle impostazioni del modello di Key Vault, vedere informazioni di [riferimento sui modelli ARM di Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Altri modelli di Key Vault Gestione risorse disponibili

Per gli oggetti Key Vault sono disponibili altri modelli di Gestione risorse:

| Segreti | Chiavi | Certificati |
|--|--|--|
|[Guida introduttiva](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Riferimento](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Altri modelli di Key Vault disponibili qui: [Key Vault gestione risorse Reference](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Distribuire i modelli

È possibile usare la portale di Azure per distribuire i modelli sopra indicati usando l'opzione ' Crea modello personalizzato nell'Editor ' nella Guida seguente: [distribuire le risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

È anche possibile salvare i modelli sopra indicati nei file e usare i comandi seguenti:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare le guide introduttive e le esercitazioni successive, è possibile lasciare invariate le risorse. Quando le risorse non sono più necessarie, eliminare il gruppo di risorse, che elimina l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure o Azure PowerShell, seguire questa procedura.

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

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)
- [Eseguire l'autenticazione in un insieme di credenziali delle chiavi](authentication.md)
- [Guida per gli sviluppatori di Azure Key Vault](developers-guide.md)
