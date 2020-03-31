---
title: Crittografia dei dati - Portale di Azure - Database di Azure per MySQLData encryption - Azure portal - Azure Database for MySQL
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per MySQL usando il portale di Azure.Learn how to set up and manage data encryption for your Azure Database for MySQL by using the Azure portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299040"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Crittografia dei dati per il database di Azure per MySQL tramite il portale di AzureData encryption for Azure Database for MySQL by using the Azure portal

Informazioni su come usare il portale di Azure per configurare e gestire la crittografia dei dati per il database di Azure per MySQL.Learn how to use the Azure portal to set up and manage data encryption for your Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di AzurePrerequisites for

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* In Archiviazione delle chiavi di Azure creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere le proprietà seguenti da utilizzare come chiave gestita dal cliente:The key vault must have the following properties to use as a customer-managed key:
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
  * In grado di eseguire le operazioni Ottieni, Esegui il wrapping della chiave, Annulla il wrapping della chiave

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. Nell'insieme di credenziali delle chiavi selezionare **Criteri** > di accesso**Aggiungi criteri di accesso**.

   ![Screenshot dell'insieme di credenziali delle chiavi, con i criteri di accesso e l'opzione Aggiungi criteri di accesso evidenziati](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selezionare **Autorizzazioni chiave**e selezionare **Get**, **Wrap**, **Unwrap**e **Principal**, ovvero il nome del server MySQL. Se l'entità server non viene trovata nell'elenco delle entità esistenti, è necessario registrarla. Viene richiesto di registrare l'entità server quando si tenta di impostare la crittografia dei dati per la prima volta e l'operazione non riesce.

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selezionare **Salva**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Impostare la crittografia dei dati per il database di Azure per MySQLSet data encryption for Azure Database for MySQL

1. In Database di Azure per MySQL selezionare **Crittografia dati** per configurare la chiave gestita dal cliente.

   ![Screenshot del database di Azure per MySQL, con crittografia dei dati evidenziata](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. È possibile selezionare un insieme di credenziali delle chiavi e una coppia di chiavi oppure immettere un identificatore di chiave.

   ![Screenshot del database di Azure per MySQL, con le opzioni di crittografia dei dati evidenziate](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selezionare **Salva**.

4. Per assicurarsi che tutti i file (inclusi i file temporanei) siano completamente crittografati, riavviare il server.

## <a name="restore-or-create-a-replica-of-the-server"></a>Ripristinare o creare una replica del server

Dopo aver crittografato il database di Azure per MySQL con la chiave gestita di un cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. È possibile eseguire questa nuova copia tramite un'operazione di ripristino locale o geografico oppure tramite un'operazione di replica (locale/tra aree). Quindi, per un server MySQL crittografato, è possibile utilizzare la seguente procedura per creare un server ripristinato crittografato.

1. Nel server selezionare**Ripristino** **generale** > .

   ![Screenshot del database di Azure per MySQL, con l'opzione Panoramica e ripristino evidenziata](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server abilitato alla replica, selezionare **Replica**sotto l'intestazione **Impostazioni.**

   ![Screenshot del database di Azure per MySQL, con la replica evidenziata](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato viene crittografato con la chiave del server primario. Tuttavia, le funzionalità e le opzioni sul server sono disabilitate e il server non è accessibile. Ciò impedisce qualsiasi manipolazione dei dati, perché all'identità del nuovo server non è stata ancora concessa l'autorizzazione per accedere all'insieme di credenziali delle chiavi.

   ![Screenshot del database di Azure per MySQL, con lo stato Inaccessibile evidenziato](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per rendere accessibile il server, riconvalidare la chiave sul server ripristinato. Selezionare Chiave di**riconvalidazione** **crittografia** > dati .

   > [!NOTE]
   > Il primo tentativo di riconvalida avrà esito negativo, perché l'entità servizio del nuovo server deve avere accesso all'insieme di credenziali delle chiavi. Per generare l'entità servizio, selezionare **Riconvalida chiave**, che mostrerà un errore ma genera l'entità servizio. In seguito, fare riferimento a [questi passaggi](#set-the-right-permissions-for-key-operations) descritti in precedenza in questo articolo.

   ![Screenshot del database di Azure per MySQL, con il passaggio di riconvalida evidenziato](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere all'insieme di credenziali delle chiavi l'accesso al nuovo server.

4. Dopo aver registrato l'entità servizio, riconvalidare nuovamente la chiave e il server riprende la normale funzionalità.

   ![Screenshot del database di Azure per MySQL, che mostra le funzionalità ripristinate](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Uso di un modello di Azure Resource Manager per abilitare la crittografia dei datiUsing an Azure Resource Manager template to enable data encryption

Oltre al portale di Azure, è anche possibile abilitare la crittografia dei dati nel database di Azure per il server MySQL usando i modelli di Azure Resource Manager per i server nuovi ed esistenti.

### <a name="for-a-new-server"></a>Per un nuovo server

Usare uno dei modelli di Azure Resource Manager creati in precedena per eseguire il provisioning del server con la crittografia dei dati abilitata: [Esempio con crittografia dei datiUse](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption) one of the pre-created Azure Resource Manager templates to provision the server with data encryption enabled: Example with Data encryption

Questo modello di Azure Resource Manager crea un database di Azure per il server MySQL e usa **KeyVault** e **Key** passate come parametri per abilitare la crittografia dei dati nel server.

### <a name="for-an-existing-server"></a>Per un server esistente
Inoltre, è possibile usare i modelli di Azure Resource Manager per abilitare la crittografia dei dati nel database di Azure esistente per i server MySQL.Additionally, you can use Azure Resource Manager templates to enable data encryption on your existing Azure Database for MySQL servers.

* Passare l'URI della chiave dell'insieme di `keyVaultKeyUri` credenziali delle chiavi di Azure copiato in precedenza nella proprietà nell'oggetto properties.

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
