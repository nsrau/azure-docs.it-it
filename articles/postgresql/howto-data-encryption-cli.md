---
title: Crittografia dei dati-interfaccia della riga di comando di Azure-per database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7494135cd4912ec8e59a32592ebcca0e0a6813b0
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797815"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Crittografia dei dati per il server singolo di database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure

Informazioni su come usare l'interfaccia della riga di comando di Azure per configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* Creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente. Abilitare anche l'eliminazione della protezione e l'eliminazione temporanea nell'insieme di credenziali delle chiavi.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* Nel Azure Key Vault creato creare la chiave che verrà usata per la crittografia dei dati del server singolo del database di Azure per PostgreSQL.

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
  * Eseguire operazioni **Get**, **Wrap** e **Unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. Esistono due modi per ottenere l'identità gestita per il server singolo del database di Azure per PostgreSQL.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Creare un nuovo database di Azure per il server PostgreSQL con un'identità gestita.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Aggiornare un database di Azure per il server PostgreSQL esistente per ottenere un'identità gestita.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Impostare le **autorizzazioni chiave** (**Get**, **Wrap**, **Unwrap**) per l' **entità**, che corrisponde al nome del server a server singolo PostgreSQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Impostare la crittografia dei dati per il server singolo del database di Azure per PostgreSQL

1. Abilitare la crittografia dei dati per il server singolo del database di Azure per PostgreSQL usando la chiave creata nel Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    URL chiave:`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Uso della crittografia dei dati per i server di ripristino o di replica

Una volta crittografato il server singolo di Database di Azure per PostgreSQL con una chiave gestita dal cliente archiviata in Key Vault, viene crittografata anche qualsiasi nuova copia creata del server. Questa nuova copia può essere eseguita tramite un'operazione di ripristino locale o geografica oppure tramite un'operazione di replica (locale/tra aree). Quindi, per un server a server singolo PostgreSQL crittografato, è possibile usare la procedura seguente per creare un server ripristinato crittografato.

### <a name="creating-a-restoredreplica-server"></a>Creazione di un server ripristinato/di replica

* [Creazione di un server di ripristino](howto-restore-server-cli.md)
* [Creare un server di replica di lettura](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Dopo il ripristino del server, riconvalida dati crittografia del server ripristinato

*   Assegnare l'identità per il server di replica
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Ottenere la chiave esistente da usare per il server ripristinato/di replica

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Impostare i criteri per la nuova identità per il server ripristinato/di replica

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Eseguire nuovamente la convalida del server ripristinato/di replica con la chiave di crittografia

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Funzionalità aggiuntiva per la chiave usata per il server singolo del database di Azure per PostgreSQL

### <a name="get-the-key-used"></a>Ottenere la chiave utilizzata

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

URL chiave:`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Elencare la chiave utilizzata

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Elimina la chiave utilizzata

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Uso di un modello di Azure Resource Manager per abilitare la crittografia dei dati

Oltre portale di Azure, è anche possibile abilitare la crittografia dei dati nel database di Azure per il server singolo PostgreSQL usando modelli Azure Resource Manager per il server nuovo ed esistente.

### <a name="for-a-new-server"></a>Per un nuovo server

Usare uno dei modelli di Azure Resource Manager creati in precedenza per eseguire il provisioning del server con la crittografia dei dati abilitata: [esempio con la crittografia dei dati](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Questo modello di Azure Resource Manager crea un server singolo database di Azure per PostgreSQL e **Usa l'insieme** di credenziali delle chiavi e la **chiave** passati come parametri per abilitare la crittografia dei dati nel server.

### <a name="for-an-existing-server"></a>Per un server esistente
Inoltre, è possibile usare i modelli di Azure Resource Manager per abilitare la crittografia dei dati nei server singoli del database di Azure per PostgreSQL.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Per altre informazioni sulla crittografia dei dati, vedere [crittografia dei dati a server singolo di database di Azure per PostgreSQL con chiave gestita dal cliente](concepts-data-encryption-postgresql.md).
