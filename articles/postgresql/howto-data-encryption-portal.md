---
title: Crittografia dei dati-portale di Azure per database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL usando il portale di Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81457978"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Crittografia dei dati per il server singolo database di Azure per PostgreSQL usando il portale di Azure

Informazioni su come usare la portale di Azure per configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL.

## <a name="prerequisites-for-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* In Azure Key Vault creare un insieme di credenziali delle chiavi e una chiave da usare per una chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere le proprietà seguenti da usare come chiave gestita dal cliente:
  * [Eliminazione temporanea](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Ripulitura protetta](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere gli attributi seguenti da usare come chiave gestita dal cliente:
  * Nessuna data di scadenza
  * Non disabilitato
  * In grado di eseguire operazioni get, wrap Key e unwrap Key

## <a name="set-the-right-permissions-for-key-operations"></a>Impostare le autorizzazioni appropriate per le operazioni chiave

1. In Key Vault selezionare **criteri di accesso**  >  **Aggiungi criteri di accesso**.

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

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Uso della crittografia dei dati per i server di ripristino o di replica

Una volta crittografato il server singolo di Database di Azure per PostgreSQL con una chiave gestita dal cliente archiviata in Key Vault, viene crittografata anche qualsiasi nuova copia creata del server. Questa nuova copia può essere eseguita tramite un'operazione di ripristino locale o geografica oppure tramite un'operazione di replica (locale/tra aree). Per un server PostgreSQL crittografato, è quindi possibile usare la procedura seguente per creare un server ripristinato crittografato.

1. Nel server selezionare **Panoramica**  >  **ripristino**.

   ![Screenshot del database di Azure per PostgreSQL con panoramica e ripristino evidenziati](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server abilitato per la replica, selezionare **replica**nell'intestazione **Impostazioni** .

   ![Screenshot del database di Azure per PostgreSQL con la replica evidenziata](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato verrà crittografato con la chiave del server primario. Tuttavia, le funzionalità e le opzioni del server sono disabilitate e il server è inaccessibile. In questo modo si evita la manipolazione dei dati, perché all'identità del nuovo server non è ancora stata assegnata l'autorizzazione per accedere all'insieme di credenziali delle chiavi.

   ![Screenshot del database di Azure per PostgreSQL, con stato inaccessibile evidenziato](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per rendere accessibile il server, rivalidare la chiave nel server ripristinato. Selezionare **Data Encryption**  >  **chiave di riconvalida**crittografia dati.

   > [!NOTE]
   > Il primo tentativo di riconvalida avrà esito negativo perché l'entità servizio del nuovo server deve avere accesso all'insieme di credenziali delle chiavi. Per generare l'entità servizio, selezionare **revalidate Key**, che visualizzerà un errore ma genera l'entità servizio. Successivamente, fare riferimento a [questi passaggi descritti](#set-the-right-permissions-for-key-operations) in precedenza in questo articolo.

   ![Screenshot del database di Azure per PostgreSQL, con il passaggio di riconvalida evidenziato](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere all'insieme di credenziali delle chiavi l'accesso al nuovo server.

4. Dopo la registrazione dell'entità servizio, riconvalidare nuovamente la chiave e il server riprende le funzionalità normali.

   ![Screenshot del database di Azure per PostgreSQL che mostra la funzionalità ripristinata](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [crittografia dei dati a server singolo di database di Azure per PostgreSQL con chiave gestita dal cliente](concepts-data-encryption-postgresql.md).
