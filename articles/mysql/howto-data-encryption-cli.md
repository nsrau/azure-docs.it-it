---
title: Crittografia dei dati-interfaccia della riga di comando di Azure-database di Azure per MySQL
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07d2e9fa98c24695a119c651539d4003ecd8524a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242093"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Crittografia dei dati per database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure

Informazioni su come usare l'interfaccia della riga di comando di Azure per configurare e gestire la crittografia dei dati per il database di Azure per MySQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* Creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente. Abilitare anche l'eliminazione della protezione e l'eliminazione temporanea nell'insieme di credenziali delle chiavi.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true -enable-purge-protection true
  ```

* Nel Azure Key Vault creato creare la chiave che verrà usata per la crittografia dei dati del database di Azure per MySQL.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* Per usare un insieme di credenziali delle chiavi esistente, deve avere le proprietà seguenti da usare come chiave gestita dal cliente:

  * [Eliminazione temporanea](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Ripulitura protetta](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere gli attributi seguenti da usare come chiave gestita dal cliente:
  * Nessuna data di scadenza
  * Non disabilitato
  * Eseguire operazioni **Get** , **Wrap** e **Unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. Esistono due modi per ottenere l'identità gestita per il database di Azure per MySQL.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Creare un nuovo database di Azure per il server MySQL con un'identità gestita.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Aggiornare un database di Azure per il server MySQL esistente per ottenere un'identità gestita.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. Impostare le **autorizzazioni chiave** ( **Get** , **Wrap** , **Unwrap** ) per l' **entità** , che corrisponde al nome del server MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Impostare la crittografia dei dati per database di Azure per MySQL

1. Abilitare la crittografia dei dati per il database di Azure per MySQL usando la chiave creata nel Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    URL chiave:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Uso della crittografia dei dati per i server di ripristino o di replica

Una volta eseguita la crittografia di Database di Azure per MySQL con una chiave gestita dal cliente archiviata in Key Vault, viene crittografata anche qualsiasi nuova copia creata del server. Questa nuova copia può essere eseguita tramite un'operazione di ripristino locale o geografica oppure tramite un'operazione di replica (locale/tra aree). Per un server MySQL crittografato, è quindi possibile usare la procedura seguente per creare un server ripristinato crittografato.

### <a name="creating-a-restoredreplica-server"></a>Creazione di un server ripristinato/di replica

* [Creazione di un server di ripristino](howto-restore-server-cli.md) 
* [Creare un server di replica di lettura](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Dopo il ripristino del server, riconvalida dati crittografia del server ripristinato

*   Assegnare l'identità per il server di replica
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Ottenere la chiave esistente da usare per il server ripristinato/di replica

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Impostare i criteri per la nuova identità per il server ripristinato/di replica
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Eseguire nuovamente la convalida del server ripristinato/di replica con la chiave di crittografia

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Funzionalità aggiuntiva per la chiave usata per il database di Azure per MySQL

### <a name="get-the-key-used"></a>Ottenere la chiave utilizzata

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

URL chiave: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Elencare la chiave utilizzata

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Elimina la chiave utilizzata

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Uso di un modello di Azure Resource Manager per abilitare la crittografia dei dati

Oltre alla portale di Azure, è anche possibile abilitare la crittografia dei dati nel database di Azure per il server MySQL usando modelli Azure Resource Manager per i server nuovi ed esistenti.

### <a name="for-a-new-server"></a>Per un nuovo server

Usare uno dei modelli di Azure Resource Manager creati in precedenza per eseguire il provisioning del server con la crittografia dei dati abilitata: [esempio con la crittografia dei dati](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Questo modello di Azure Resource Manager crea un database di Azure per il server MySQL e **Usa l'insieme** di credenziali delle chiavi e la **chiave** passati come parametri per abilitare la crittografia dei dati nel server.

### <a name="for-an-existing-server"></a>Per un server esistente

Inoltre, è possibile usare i modelli di Azure Resource Manager per abilitare la crittografia dei dati nel database di Azure per i server MySQL.

* Passare l'ID risorsa della chiave di Azure Key Vault copiata in precedenza sotto la `Uri` proprietà nell'oggetto Properties.

* Usare *2020-01-01-Preview* come versione dell'API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [crittografia dei dati di database di Azure per MySQL con chiave gestita dal cliente](concepts-data-encryption-mysql.md).
