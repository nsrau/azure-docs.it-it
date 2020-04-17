---
title: Crittografia dei dati - Portale di Azure - per Il database di Azure per PostgreSQL - Server singoloData encryption - Azure portal - for Azure Database for PostgreSQL - Single server
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per PostgreSQL Singolo server tramite il portale di Azure.Learn how to set up and manage data encryption for your Azure Database for PostgreSQL Single server by using the Azure portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457978"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Crittografia dei dati per il database di Azure per PostgreSQL Singolo server tramite il portale di AzureData encryption for Azure Database for PostgreSQL Single server by using the Azure portal

Informazioni su come usare il portale di Azure per configurare e gestire la crittografia dei dati per il database di Azure per PostgreSQL Singolo server.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di AzurePrerequisites for

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* In Archiviazione delle chiavi di Azure creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere le proprietà seguenti da utilizzare come chiave gestita dal cliente:The key vault must have the following properties to use as a customer-managed key:
  * [Eliminazione temporanea](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Eliminatesse protette](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere i seguenti attributi da utilizzare come chiave gestita dal cliente:
  * Nessuna data di scadenza
  * Non disabilitato
  * In grado di eseguire operazioni get, wrap key e unwrap key

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. Nell'insieme di credenziali delle chiavi selezionare **Criteri** > di accesso**Aggiungi criteri di accesso**.

   ![Screenshot dell'insieme di credenziali delle chiavi, con i criteri di accesso e l'opzione Aggiungi criteri di accesso evidenziati](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selezionare **Autorizzazioni chiave**e selezionare **Get**, **Wrap**, **Unwrap**e **Principal**, ovvero il nome del server PostgreSQL . Se l'entità server non viene trovata nell'elenco delle entità esistenti, è necessario registrarla. Viene richiesto di registrare l'entità server quando si tenta di impostare la crittografia dei dati per la prima volta e l'operazione non riesce.  

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selezionare **Salva**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Impostare la crittografia dei dati per il database di Azure per PostgreSQL singolo serverSet data encryption for Azure Database for PostgreSQL Single server

1. In Database di Azure per PostgreSQL selezionare **Crittografia dati** per configurare la chiave gestita dal cliente.

   ![Screenshot del database di Azure per PostgreSQL, con la crittografia dei dati evidenziata](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. È possibile selezionare un insieme di credenziali delle chiavi e una coppia di chiavi oppure immettere un identificatore di chiave.

   ![Screenshot del database di Azure per PostgreSQL, con le opzioni di crittografia dei dati evidenziate](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selezionare **Salva**.

4. Per assicurarsi che tutti i file (inclusi i file temporanei) siano completamente crittografati, riavviare il server.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilizzo della crittografia dei dati per il ripristino o i server di replicaUsing Data encryption for restore or replica servers

Dopo che il database di Azure per PostgreSQL Singolo server viene crittografato con la chiave gestita di un cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. È possibile eseguire questa nuova copia tramite un'operazione di ripristino locale o geografico oppure tramite un'operazione di replica (locale/tra aree). Quindi, per un server PostgreSQL crittografato, è possibile utilizzare la procedura seguente per creare un server ripristinato crittografato.

1. Nel server selezionare**Ripristino** **generale** > .

   ![Screenshot del database di Azure per PostgreSQL, con l'opzione Panoramica e ripristino evidenziata](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server abilitato alla replica, selezionare **Replica**sotto l'intestazione **Impostazioni.**

   ![Screenshot del database di Azure per PostgreSQL, con la replica evidenziata](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato viene crittografato con la chiave del server primario. Tuttavia, le funzionalità e le opzioni sul server sono disabilitate e il server non è accessibile. Ciò impedisce qualsiasi manipolazione dei dati, perché all'identità del nuovo server non è stata ancora concessa l'autorizzazione per accedere all'insieme di credenziali delle chiavi.

   ![Screenshot del database di Azure per PostgreSQL, con lo stato Inaccessibile evidenziato](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per rendere accessibile il server, riconvalidare la chiave sul server ripristinato. Selezionare Chiave di**riconvalida** **crittografia** > dati .

   > [!NOTE]
   > Il primo tentativo di riconvalida avrà esito negativo, perché l'entità servizio del nuovo server deve avere accesso all'insieme di credenziali delle chiavi. Per generare l'entità servizio, selezionare **Riconvalida chiave**, che mostrerà un errore ma genera l'entità servizio. In seguito, fare riferimento a [questi passaggi](#set-the-right-permissions-for-key-operations) descritti in precedenza in questo articolo.

   ![Screenshot del database di Azure per PostgreSQL, con il passaggio di riconvalida evidenziato](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere all'insieme di credenziali delle chiavi l'accesso al nuovo server.

4. Dopo aver registrato l'entità servizio, riconvalidare nuovamente la chiave e il server riprende la normale funzionalità.

   ![Screenshot del database di Azure per PostgreSQL, che mostra le funzionalità ripristinate](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [Database di Azure per PostgreSQL Crittografia dati server singolo con chiave gestita dal cliente](concepts-data-encryption-postgresql.md).
