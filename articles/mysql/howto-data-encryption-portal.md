---
title: Crittografia dei dati - Portale di Azure - Database di Azure per MySQLData encryption - Azure portal - Azure Database for MySQL
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per MySQL usando il portale di Azure.Learn how to set up and manage data encryption for your Azure Database for MySQL by using the Azure portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9d1e89919647d9d94b287618da2f9a77278425a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459084"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Crittografia dei dati per il database di Azure per MySQL tramite il portale di AzureData encryption for Azure Database for MySQL by using the Azure portal

Informazioni su come usare il portale di Azure per configurare e gestire la crittografia dei dati per il database di Azure per MySQL.Learn how to use the Azure portal to set up and manage data encryption for your Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di AzurePrerequisites for

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* In Archiviazione delle chiavi di Azure creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere le proprietà seguenti da utilizzare come chiave gestita dal cliente:The key vault must have the following properties to use as a customer-managed key:
  * [Eliminazione temporanea](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Eliminatesse protette](../key-vault/general/overview-soft-delete.md#purge-protection)

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

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilizzo della crittografia dei dati per il ripristino o i server di replicaUsing Data encryption for restore or replica servers

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

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere Crittografia dei dati di [Azure Database for MySQL con chiave gestita dal cliente.](concepts-data-encryption-mysql.md)
