---
title: Crittografia dei dati-portale di Azure per database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL usando il portale di Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297068"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Crittografia dei dati per il server singolo database di Azure per PostgreSQL usando il portale di Azure

Informazioni su come usare la portale di Azure per configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* In Azure Key Vault creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere le proprietà seguenti da usare come chiave gestita dal cliente:
  * [Eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Ripulitura protetta](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere gli attributi seguenti da usare come chiave gestita dal cliente:
  * Nessuna data di scadenza
  * Non disabilitato
  * In grado di eseguire operazioni get, wrap Key e unwrap Key

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. In Key Vault selezionare **criteri di accesso** > **Aggiungi criteri di accesso**.

   ![Screenshot di Key Vault, con criteri di accesso e Aggiungi criteri di accesso evidenziati](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selezionare **autorizzazioni chiave**e selezionare **Get**, **Wrap**, **Unwrap**e l' **entità**, che corrisponde al nome del server PostgreSQL. Se non è possibile trovare l'entità server nell'elenco di entità esistenti, è necessario registrarla. Viene richiesto di registrare l'entità server quando si tenta di configurare la crittografia dei dati per la prima volta e l'operazione ha esito negativo.  

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selezionare **Salva**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Impostare la crittografia dei dati per il server singolo del database di Azure per PostgreSQL

1. In database di Azure per PostgreSQL selezionare **crittografia dei dati** per configurare la chiave gestita dal cliente.

   ![Screenshot del database di Azure per PostgreSQL con la crittografia dei dati evidenziata](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. È possibile selezionare un insieme di credenziali delle chiavi e una coppia di chiavi oppure immettere un identificatore di chiave.

   ![Screenshot del database di Azure per PostgreSQL con le opzioni di crittografia dei dati evidenziate](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selezionare **Salva**.

4. Per assicurarsi che tutti i file, inclusi i file temporanei, siano completamente crittografati, riavviare il server.

## <a name="restore-or-create-a-replica-of-the-server"></a>Ripristinare o creare una replica del server

Dopo che il server singolo di database di Azure per PostgreSQL è stato crittografato con una chiave gestita del cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. Questa nuova copia può essere eseguita tramite un'operazione di ripristino locale o geografica oppure tramite un'operazione di replica (locale/tra aree). Per un server PostgreSQL crittografato, è quindi possibile usare la procedura seguente per creare un server ripristinato crittografato.

1. Nel server selezionare **panoramica** > **Ripristina**.

   ![Screenshot del database di Azure per PostgreSQL con panoramica e ripristino evidenziati](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server abilitato per la replica, selezionare **replica**nell'intestazione **Impostazioni** .

   ![Screenshot del database di Azure per PostgreSQL con la replica evidenziata](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato verrà crittografato con la chiave del server primario. Tuttavia, le funzionalità e le opzioni del server sono disabilitate e il server è inaccessibile. In questo modo si evita la manipolazione dei dati, perché all'identità del nuovo server non è ancora stata assegnata l'autorizzazione per accedere all'insieme di credenziali delle chiavi.

   ![Screenshot del database di Azure per PostgreSQL, con stato inaccessibile evidenziato](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per rendere accessibile il server, rivalidare la chiave nel server ripristinato. Selezionare **crittografia dati** > **chiave di riconvalida**.

   > [!NOTE]
   > Il primo tentativo di riconvalida avrà esito negativo perché l'entità servizio del nuovo server deve avere accesso all'insieme di credenziali delle chiavi. Per generare l'entità servizio, selezionare **revalidate Key**, che visualizzerà un errore ma genera l'entità servizio. Successivamente, fare riferimento a [questi passaggi descritti](#set-the-right-permissions-for-key-operations) in precedenza in questo articolo.

   ![Screenshot del database di Azure per PostgreSQL, con il passaggio di riconvalida evidenziato](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere all'insieme di credenziali delle chiavi l'accesso al nuovo server.

4. Dopo la registrazione dell'entità servizio, riconvalidare nuovamente la chiave e il server riprende le funzionalità normali.

   ![Screenshot del database di Azure per PostgreSQL che mostra la funzionalità ripristinata](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Uso di un modello di Azure Resource Manager per abilitare la crittografia dei dati

Oltre portale di Azure, è anche possibile abilitare la crittografia dei dati nel database di Azure per il server singolo PostgreSQL usando modelli Azure Resource Manager per il server nuovo ed esistente.

### <a name="for-a-new-server"></a>Per un nuovo server

Usare uno dei modelli di Azure Resource Manager creati in precedenza per eseguire il provisioning del server con la crittografia dei dati abilitata: [esempio con la crittografia dei dati](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Questo modello di Azure Resource Manager crea un server singolo database di Azure per PostgreSQL e **Usa l'insieme** di credenziali delle chiavi e la **chiave** passati come parametri per abilitare la crittografia dei dati nel server.

### <a name="for-an-existing-server"></a>Per un server esistente
Inoltre, è possibile usare i modelli di Azure Resource Manager per abilitare la crittografia dei dati nei server singoli del database di Azure per PostgreSQL.

* Passare l'URI della chiave di Azure Key Vault copiata in precedenza sotto la proprietà `keyVaultKeyUri` nell'oggetto Properties.

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
