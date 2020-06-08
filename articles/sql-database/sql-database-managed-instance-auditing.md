---
title: Controllo delle istanze gestite
description: Informazioni su come iniziare a eseguire il controllo dell'istanza gestita del database SQL di Azure usando T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 66c2c5e52ef81f74f7177af897f33eec4ae1779e
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860644"
---
# <a name="azure-sql-database-managed-instance-auditing"></a>Controllare un'istanza gestita di Database SQL di Azure

Il controllo dell'[istanza gestita](sql-database-managed-instance.md) tiene traccia degli eventi del database e li registra in un log di controllo nell'account di archiviazione di Azure. Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.
- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per ulteriori informazioni sui programmi Azure che supportano la conformità agli standard, vedere il [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), dove è possibile trovare l'elenco più recente di certificazioni di conformità del database SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurare il controllo per il server in Archiviazione di Azure

La sezione seguente descrive la configurazione del controllo per l'istanza gestita.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Creare un **contenitore** di Archiviazione di Azure in cui archiviare i log di controllo.

   1. Passare all'archiviazione di Azure in cui si vogliono archiviare i log di controllo.

      > [!IMPORTANT]
      > - Usare un account di archiviazione nella stessa area dell'istanza gestita per evitare operazioni di lettura e scrittura tra aree diverse. 
      > - Se l'account di archiviazione si trova dietro una rete virtuale o un firewall, vedere [Concedere l'accesso da una rete virtuale](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).
      > - Se si modifica il periodo di conservazione da 0 (conservazione illimitata) a qualsiasi altro valore, tenere presente che la conservazione verrà applicata solo ai log scritti dopo la modifica del valore di conservazione (i log scritti durante il periodo in cui la conservazione è stata impostata su illimitata vengono conservati, anche dopo l'abilitazione della conservazione).

   1. Nell'account di archiviazione passare a **Panoramica** e fare clic su **BLOB**.

      ![Widget BLOB di Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Nel menu in alto fare clic su **+ Contenitore** per creare un nuovo contenitore.

      ![Icona per creare un contenitore BLOB](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Specificare un **Nome** per il contenitore, impostare Livello di accesso pubblico su **Privato** e quindi fare clic su **OK**.

      ![Creare una configurazione del contenitore BLOB](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Il cliente che desidera configurare un archivio di log non modificabile per gli eventi di controllo a livello di server o di database deve seguire le istruzioni [fornite da Archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (assicurarsi di aver selezionato **Consenti aggiunte aggiuntive** quando si configura l'archivio BLOB non modificabile)
  
3. Dopo aver creato il contenitore, è possibile configurarlo come destinazione dei log di controllo in due modi: [usando T-SQL](#blobtsql) oppure [tramite l'interfaccia utente di SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Configurare l'archiviazione BLOB per i log di controllo usando T-SQL:

     1. Nell'elenco dei contenitori fare clic sul contenitore appena creato e quindi fare clic su **Proprietà del contenitore**.

        ![Pulsante delle proprietà del contenitore BLOB](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copiare l'URL del contenitore facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro. Il formato dell'URL del contenitore deve essere `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL da copiare del contenitore BLOB](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generare un **token di firma di accesso condiviso** di Archiviazione di Azure per concedere all'account di archiviazione i diritti di accesso al controllo dell'istanza gestita:

        - Passare all'account di Archiviazione di Azure in cui è stato creato il contenitore nel passaggio precedente.

        - Fare clic su **Firma di accesso condiviso** nel menu Impostazioni di archiviazione.

          ![Icona Firma di accesso condiviso nel menu Impostazioni di archiviazione](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configurare SAS come segue:

          - **Servizi consentiti**: BLOB

          - **Data di inizio**: per evitare problemi correlati al fuso orario, è consigliabile usare la data del giorno precedente

          - **Data di fine**: scegliere la data di scadenza del token di firma di accesso condiviso

            > [!NOTE]
            > Rinnovare il token alla scadenza per evitare errori di controllo.

          - Fare clic su **Genera firma di accesso condiviso**.
            
            ![Configurazione della firma di accesso condiviso](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Fare clic su Genera firma di accesso condiviso. Il token verrà visualizzato in basso. Copiare il token facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro.

          ![Copiare il token di accesso condiviso](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Rimuovere il carattere punto interrogativo ("?") dall'inizio del token.

     1. Connettersi all'istanza gestita tramite SQL Server Management Studio (SSMS) o qualsiasi altro strumento supportato.

     1. Eseguire l'istruzione T-SQL seguente per **creare nuove credenziali** usando l'URL del contenitore e il token di firma di accesso condiviso creati nei passaggi precedenti:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Eseguire l'istruzione T-SQL seguente per creare un nuovo controllo server. Scegliere un nome personalizzato per il controllo e usare l'URL del contenitore creato nei passaggi precedenti. Se non specificato diversamente, l'impostazione predefinita di `RETENTION_DAYS` è 0 (conservazione illimitata):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Proseguire con la [creazione di una specifica di controllo server o una specifica di controllo database](#createspec)

   - <a id="blobssms"></a>Configurare l'archiviazione BLOB per i log di controllo tramite SQL Server Management Studio (SSMS) 18 (anteprima):

     1. Connettersi all'istanza gestita tramite l'interfaccia utente di SQL Server Management Studio (SSMS).

     1. Espandere il nodo radice di Esplora oggetti.

     1. Espandere il nodo **Sicurezza**, fare clic con il pulsante destro del mouse sul nodo **Controlli** e scegliere "Nuovo controllo":

        ![Espandere i nodi Sicurezza e Controlli](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Verificare che in **Destinazione controllo** sia selezionata l'opzione "URL" e fare clic su **Sfoglia**:

        ![Sfogliare Archiviazione di Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Facoltativo) Accedere all'account di Azure:

        ![Accedere ad Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selezionare una sottoscrizione, un account di archiviazione e un contenitore BLOB dagli elenchi a discesa oppure creare un contenitore personalizzato facendo clic su **Crea**. Al termine, fare clic su **OK**:

        ![Selezionare la sottoscrizione di Azure, l'account di archiviazione e il contenitore BLOB](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Fare clic su **OK** nella finestra di dialogo "Crea controllo".

4. <a id="createspec"></a>Dopo aver configurato il contenitore BLOB come destinazione dei log di controllo, creare e abilitare una specifica di controllo server o una specifica di controllo database come nel caso di SQL Server:

   - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Abilitare il controllo server creato nel passaggio 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Per altre informazioni:

- [Differenze di controllo tra database singoli, pool elastici e istanze gestite nel database SQL di Azure e i database in SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurare il controllo per il server in Hub eventi o Log di Monitoraggio di Azure

I log di controllo provenienti da un'istanza gestita possono essere inviati ad Hub eventi o Log di Monitoraggio di Azure. Questa sezione illustra come configurarli:

1. All'interno del [portale di Azure](https://portal.azure.com/) passare all'istanza gestita.

2. Fare clic su **Impostazioni di diagnostica**.

3. Fare clic su **Attiva diagnostica**. Se la diagnostica è già abilitata verrà invece visualizzata l'opzione *+ Aggiungi impostazione di diagnostica*.

4. Selezionare **SQLSecurityAuditEvents** nell'elenco dei log.

5. Selezionare una destinazione per gli eventi di controllo: Hub eventi, Log di Monitoraggio di Azure o entrambi. Configurare per ogni destinazione i parametri obbligatori (ad esempio Log Analytics dell'area di lavoro).

6. Fare clic su **Salva**.

    ![Configurare le impostazioni di diagnostica](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Connettersi all'istanza gestita usando **SQL Server Management Studio (SSMS)** o qualsiasi altro client supportato.

8. Eseguire l'istruzione T-SQL seguente per creare un controllo del server:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Creare e abilitare una specifica di controllo server o di controllo database come si farebbe per SQL Server:

   - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Abilitare il controllo server creato nel passaggio 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Utilizzo di log di controllo

### <a name="consume-logs-stored-in-azure-storage"></a>Utilizzo dei log archiviati in Archiviazione di Azure

Per visualizzare i log di controllo dei BLOB sono disponibili diversi metodi.

- Usare la funzione di sistema `sys.fn_get_audit_file` (T-SQL) per restituire i dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere la [documentazione su sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). In Archiviazione di Azure i log di controllo vengono salvati come raccolta di file BLOB in un contenitore appositamente definito per l'archiviazione di questi log. Per altri dettagli sulla gerarchia della cartella di archiviazione, le convenzioni di denominazione e il formato dei log, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

- Per l'elenco completo dei metodi di consumo del log di controllo, fare riferimento a [Introduzione al controllo del database SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Utilizzo dei log archiviati in Hub eventi

Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la documentazione di Hub eventi di Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Usare e analizzare i log archiviati in Log di Monitoraggio di Azure

Se i log di controllo vengono scritti in Log di Monitoraggio di Azure, saranno disponibili nell'area di lavoro Log Analytics, in cui è possibile eseguire ricerche avanzate sui dati di controllo. Come punto di partenza, passare all'area di lavoro Log Analytics e nella sezione *Generali* fare clic su *Log* e immettere una query semplice, ad esempio: `search "SQLSecurityAuditEvents"` per visualizzare i log di controllo.  

Log di Monitoraggio di Azure consente di ottenere informazioni operative in tempo reale tramite funzionalità di ricerca integrate e dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca e i comandi di Log di Monitoraggio di Azure, vedere [Guida di riferimento alla ricerca in Log di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Differenze di controllo tra i database nel database SQL di Azure e i database in SQL Server

Le principali differenze tra il controllo nei database nel database SQL di Azure e quello in SQL Server sono le seguenti:

- Con l'opzione di distribuzione dell'istanza gestita nel database SQL di Azure, il controllo viene eseguito a livello del server e archivia file di log `.xel` in Archivio BLOB di Azure.
- In SQL Server locale o nelle macchine virtuali SQL Server il controllo viene eseguito a livello del server, ma archivia gli eventi nei log eventi del file system o di Windows.

Il controllo XEvent nell'istanza gestita supporta le destinazioni di Archivio BLOB di Azure. I log di file e di Windows **non sono supportati**.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- È disponibile una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui vengono inseriti i file con estensione `.xel`.
- Viene fornita una nuova sintassi `TO EXTERNAL MONITOR` per abilitare i target Hub eventi e Log di Monitoraggio di Azure.
- La sintassi `TO FILE`**non è supportata** perché il database SQL non può accedere alle condivisioni file di Windows.
- L'opzione Shutdown **non è supportata**.
- Un valore di `queue_delay` uguale a 0 **non è supportato**.

## <a name="next-steps"></a>Passaggi successivi

- Per l'elenco completo dei metodi di consumo del log di controllo, fare riferimento a [Introduzione al controllo del database SQL](sql-database-auditing.md).
- Per ulteriori informazioni sui programmi Azure che supportano la conformità agli standard, vedere il [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), dove è possibile trovare l'elenco più recente di certificazioni di conformità del database SQL.

<!--Image references-->









