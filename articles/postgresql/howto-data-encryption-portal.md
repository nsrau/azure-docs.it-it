---
title: Crittografia dei dati per un singolo server di database di Azure per PostgreSQL tramite il portale
description: Informazioni su come configurare e gestire la crittografia dei dati per il server singolo del database di Azure per PostgreSQL usando portale di Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9c935ad8f77e2f8a6198a8ac095e0cc60c025a72
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028629"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Crittografia dei dati per un singolo server di database di Azure per PostgreSQL tramite il portale

In questo articolo si apprenderà come configurare e gestire per usare la portale di Azure per configurare la crittografia dei dati per il server singolo del database di Azure per PostgreSQL.

## <a name="prerequisites-for-cli"></a>Prerequisiti per CLI

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* Creare una Azure Key Vault e una chiave da usare per la chiave gestita dal cliente.
* L'insieme di credenziali delle chiavi deve avere la proprietà seguente da usare come chiave gestita dal cliente:
  * [eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Ripulitura protetta](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La chiave deve avere gli attributi seguenti da usare per la chiave gestita dal cliente.
  * Nessuna data di scadenza
  * Non disabilitato
  * In grado di eseguire operazioni _Get_, _Wrap Key_e _Unwrap Key_

## <a name="setting-the-right-permissions-for-key-operations"></a>Impostazione delle autorizzazioni appropriate per le operazioni principali

1. Nella Azure Key Vault selezionare i criteri di **accesso**e quindi **Aggiungi criteri di accesso**.

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. In **autorizzazioni chiave**selezionare **Get**, **Wrap**, **Unwrap** e l' **entità**, ovvero il nome del server PostgreSQL. Se non è possibile trovare l'entità server nell'elenco di entità esistenti, sarà necessario registrarla tentando di configurare la crittografia dei dati per la prima volta, operazione che avrà esito negativo.  

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Fare clic su **Save** (Salva) per salvare le impostazioni.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Impostazione della crittografia dei dati per il server singolo del database di Azure per PostgreSQL

1. Nel **database di Azure per PostgreSQL**selezionare la **crittografia dei dati** per impostare la configurazione della chiave gestita dal cliente.

   ![Impostazione della crittografia dei dati](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. È possibile selezionare un **Key Vault** e una coppia di **chiavi** oppure passare un **identificatore di chiave**.

   ![Impostazione Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Fare clic su **Save** (Salva) per salvare le impostazioni.

4. Per assicurarsi che tutti i file, inclusi **i file temporanei**, siano crittografati con crittografia completa, è **necessario** **riavviare** il server.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Ripristino o creazione della replica del server in cui è abilitata la crittografia dei dati

Una volta che un server singolo di database di Azure per PostgreSQL è crittografato con la chiave gestita del cliente archiviata nel Key Vault, qualsiasi copia appena creata del server è un'operazione locale o di ripristino geografico o un'operazione di replica (locale/tra aree). Per un server PostgreSQL crittografato, è quindi possibile seguire questa procedura per creare un server ripristinato crittografato.

1. Nel server selezionare **Panoramica**, quindi fare clic su **Ripristina**.

   ![Avvio-ripristino](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server abilitato per la replica, sotto l'intestazione **Impostazioni** selezionare **replica**, come illustrato di seguito:

   ![Avvio-replica](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato è costituito da dati crittografati con la chiave del server primario. Tuttavia, le funzionalità e le opzioni del server sono disabilitate e il server è contrassegnato in uno stato **inaccessibile** . Questo comportamento è progettato per evitare la manipolazione dei dati, perché l'identità del nuovo server non è ancora stata autorizzata ad accedere al Key Vault.

   ![Contrassegno server inaccessibile](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per correggere lo stato inaccessibile, è necessario rivalidare la chiave nel server ripristinato. Selezionare il riquadro **crittografia dati** , quindi il pulsante **riconvalida chiave** .

   > [!NOTE]
   > Il primo tentativo di riconvalida avrà esito negativo perché all'entità servizio del nuovo server deve essere concesso l'accesso all'insieme di credenziali delle chiavi. Per generare l'entità servizio, selezionare **revalidate Key**, che restituirà un errore ma genererà l'entità servizio. Successivamente, fare riferimento alla procedura descritta [nella sezione 2](#setting-the-right-permissions-for-key-operations) precedente.

   ![riconvalida server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere l'accesso al nuovo server al Key Vault.

4. Dopo aver registrato l'entità servizio, sarà necessario riconvalidare nuovamente la chiave e il server riprenderà le funzionalità normali.

   ![Server normale ripristinato](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [che cos'è la crittografia dei dati di Azure](concepts-data-encryption-postgresql.md).
