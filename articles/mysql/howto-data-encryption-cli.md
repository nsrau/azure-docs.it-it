---
title: Crittografia dei dati - interfaccia della riga di comando di Azure - Database di Azure per MySQLData encryption - Azure CLI - Azure Database for MySQL
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.Learn how to set up and manage data encryption for your Azure Database for MySQL by using the Azure CLI.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: fca9b9eea3697a10650e5dbf0522f795fe0a8e0b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522212"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Crittografia dei dati per il database di Azure per MySQL tramite l'interfaccia della riga di comando di AzureData encryption for Azure Database for MySQL by using the Azure CLI

Informazioni su come usare l'interfaccia della riga di comando di Azure per configurare e gestire la crittografia dei dati per il database di Azure per MySQL.Learn how to use the Azure CLI to set up and manage data encryption for your Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di AzurePrerequisites for

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* Creare un insieme di credenziali delle chiavi e una chiave da utilizzare per una chiave gestita dal cliente. Attivare anche la protezione dall'eliminazione definitiva e l'eliminazione temporanea nell'insieme di credenziali delle chiavi.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Nell'insieme di credenziali delle chiavi di Azure creato creare la chiave che verrà usata per la crittografia dei dati del database di Azure per MySQL.In the created Azure Key Vault, create the key that will be used for the data encryption of the Azure Database for MySQL.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Per utilizzare un insieme di credenziali delle chiavi esistente, è necessario disporre delle seguenti proprietà da utilizzare come chiave gestita dal cliente:
  * [Eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Eliminatesse protette](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere i seguenti attributi da utilizzare come chiave gestita dal cliente:
  * Nessuna data di scadenza
  * Non disabilitato
  * Eseguire operazioni **get**, **wrap**, **unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. Esistono due modi per ottenere l'identità gestita per il database di Azure per MySQL.There are two ways of getting the managed identity for your Azure Database for MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Creare un nuovo database di Azure per il server MySQL con un'identità gestita.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Aggiornare un database di Azure esistente per il server MySQL per ottenere un'identità gestita.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Impostare le **autorizzazioni chiave** (**Get**, **Wrap**, **Unwrap**) per **l'entità**, ovvero il nome del server MySQL .

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Impostare la crittografia dei dati per il database di Azure per MySQLSet data encryption for Azure Database for MySQL

1. Abilitare la crittografia dei dati per il database di Azure per MySQL usando la chiave creata nell'insieme di credenziali delle chiavi di Azure.Enable Data encryption for the Azure Database for MySQL using the key created in the Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    URL chiave:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilizzo della crittografia dei dati per il ripristino o i server di replicaUsing Data encryption for restore or replica servers

Dopo aver crittografato il database di Azure per MySQL con la chiave gestita di un cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. È possibile eseguire questa nuova copia tramite un'operazione di ripristino locale o geografico oppure tramite un'operazione di replica (locale/tra aree). Quindi, per un server MySQL crittografato, è possibile utilizzare la seguente procedura per creare un server ripristinato crittografato.

### <a name="creating-a-restoredreplica-server"></a>Creazione di un server ripristinato/replicaCreating a restored/replica server

  *  [Creare un server di ripristinoCreate a restore server](howto-restore-server-cli.md) 
  *  [Creare un server di replica di letturaCreate a read replica server](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Una volta ripristinato il server, riconvalidare la crittografia dei dati

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Funzionalità aggiuntiva per la chiave usata per il database di Azure per MySQLAdditional capability for the key being used for the Azure Database for MySQL

### <a name="get-the-key-used"></a>Ottenere la chiave utilizzata

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Elencare la chiave utilizzata

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Eliminare la chiave utilizzata

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Uso di un modello di Azure Resource Manager per abilitare la crittografia dei datiUsing an Azure Resource Manager template to enable data encryption

Oltre al portale di Azure, è anche possibile abilitare la crittografia dei dati nel database di Azure per il server MySQL usando i modelli di Azure Resource Manager per i server nuovi ed esistenti.

### <a name="for-a-new-server"></a>Per un nuovo server

Usare uno dei modelli di Azure Resource Manager creati in precedena per eseguire il provisioning del server con la crittografia dei dati abilitata: [Esempio con crittografia dei datiUse](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption) one of the pre-created Azure Resource Manager templates to provision the server with data encryption enabled: Example with Data encryption

Questo modello di Azure Resource Manager crea un database di Azure per il server MySQL e usa **KeyVault** e **Key** passate come parametri per abilitare la crittografia dei dati nel server.

### <a name="for-an-existing-server"></a>Per un server esistente
Inoltre, è possibile usare i modelli di Azure Resource Manager per abilitare la crittografia dei dati nel database di Azure esistente per i server MySQL.Additionally, you can use Azure Resource Manager templates to enable data encryption on your existing Azure Database for MySQL servers.

* Passare l'ID risorsa della chiave dell'insieme `Uri` di credenziali delle chiavi di Azure copiato in precedenza sotto la proprietà nell'oggetto properties.

* Utilizzare *2020-01-01-preview* come versione API.

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

 Per altre informazioni sulla crittografia dei dati, vedere Crittografia dei dati di [Azure Database for MySQL con chiave gestita dal cliente.](concepts-data-encryption-mysql.md)
