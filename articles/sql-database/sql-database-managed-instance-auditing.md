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
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 04/08/2019
ms.openlocfilehash: 41d632b145a7187dd0aeaab740cd8546a3955e7f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819004"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introduzione al controllo dell'istanza gestita del database SQL di Azure

Il controllo dell'[istanza gestita](sql-database-managed-instance.md) tiene traccia degli eventi del database e li registra in un log di controllo nell'account di archiviazione di Azure. Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.
- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per ulteriori informazioni sui programmi di Azure che supportano la conformità agli standard, vedere la [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurare il controllo per il server in Archiviazione di Azure

La sezione seguente descrive la configurazione del controllo per l'istanza gestita.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Creare un **contenitore** di Archiviazione di Azure in cui archiviare i log di controllo.

   1. Passare all'archiviazione di Azure in cui si vogliono archiviare i log di controllo.

      > [!IMPORTANT]
      > Usare un account di archiviazione nella stessa area dell'istanza gestita per evitare operazioni di lettura e scrittura tra aree diverse.

   1. Nell'account di archiviazione passare a **Panoramica** e fare clic su **BLOB**.

      ![Widget BLOB di Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Nel menu in alto fare clic su **+ Contenitore** per creare un nuovo contenitore.

      ![Icona per creare un contenitore BLOB](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Specificare un **Nome** per il contenitore, impostare Livello di accesso pubblico su **Privato** e quindi fare clic su **OK**.

      ![Creare una configurazione del contenitore BLOB](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Dopo aver creato il contenitore, è possibile configurarlo come destinazione dei log di controllo in due modi: [usando T-SQL](#blobtsql) oppure [tramite l'interfaccia utente di SQL Server Management Studio (SSMS)](#blobssms):

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

1. <a id="createspec"></a>Dopo aver configurato il contenitore BLOB come destinazione dei log di controllo, creare una specifica di controllo server o una specifica di controllo database come nel caso di SQL Server:

   - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Abilitare il controllo server creato nel passaggio 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Per altre informazioni:

- [Controllo delle differenze tra database singoli, pool elastici e istanze gestite in database SQL di Azure e database in SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurare il controllo per il server per l'hub eventi o i log di monitoraggio di Azure

I log di controllo da un'istanza gestita possono essere inviati anche a hub o log di monitoraggio di Azure. Questa sezione illustra come configurarli:

1. All'interno del [portale di Azure](https://portal.azure.com/) passare all'istanza gestita.

2. Fare clic su **Impostazioni di diagnostica**.

3. Fare clic su **Attiva diagnostica**. Se la diagnostica è già abilitata verrà invece visualizzata l'opzione *+ Aggiungi impostazione di diagnostica*.

4. Selezionare **SQLSecurityAuditEvents** nell'elenco dei log.

5. Selezionare una destinazione per gli eventi di controllo: Hub eventi, log di monitoraggio di Azure o entrambi. Configurare per ogni destinazione i parametri obbligatori (ad esempio Log Analytics dell'area di lavoro).

6. Fare clic su **Save**.

    ![Configurare le impostazioni di diagnostica](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Connettersi all'istanza gestita usando **SQL Server Management Studio (SSMS)** o qualsiasi altro client supportato.

8. Eseguire l'istruzione T-SQL seguente per creare un controllo del server:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Creare una specifica di controllo server o di controllo database come si farebbe per SQL Server:

   - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Abilitare il controllo del server creato nel passaggio 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
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

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Utilizzare e analizzare i log archiviati nei log di monitoraggio di Azure

Se i log di controllo vengono scritti nei log di monitoraggio di Azure, sono disponibili nell'area di lavoro Log Analytics, in cui è possibile eseguire ricerche avanzate sui dati di controllo. Come punto di partenza, passare all'area di lavoro Log Analytics e nella sezione *generale* fare clic su *log* e immettere una semplice query, ad esempio: `search "SQLSecurityAuditEvents"` per visualizzare i log di controllo.  

Log di monitoraggio di Azure offre informazioni operative in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca dei log di Azure e sui comandi, vedere i [riferimenti alla ricerca nei log di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Differenze di controllo tra i database nel database SQL di Azure e i database in SQL Server

Le principali differenze tra il controllo nei database nel database SQL di Azure e quello in SQL Server sono le seguenti:

- Con l'opzione di distribuzione dell'istanza gestita nel database SQL di Azure, il controllo viene eseguito a livello del server e archivia file di log `.xel` in Archivio BLOB di Azure.
- In SQL Server locale o nelle macchine virtuali SQL Server il controllo viene eseguito a livello del server, ma archivia gli eventi nei log eventi del file system o di Windows.

Il controllo XEvent nell'istanza gestita supporta le destinazioni di Archiviazione BLOB di Azure. I log di file e di Windows **non sono supportati**.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- È disponibile una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui vengono inseriti i file con estensione `.xel`.
- Viene fornita una nuova sintassi `TO EXTERNAL MONITOR` per abilitare anche l'hub e i log di monitoraggio di Azure.
- La sintassi `TO FILE` **non è supportata** perché il database SQL non può accedere alle condivisioni file di Windows.
- L'opzione Shutdown **non è supportata**.
- Un valore di `queue_delay` uguale a 0 **non è supportato**.

## <a name="next-steps"></a>Passaggi successivi

- Per l'elenco completo dei metodi di consumo del log di controllo, fare riferimento a [Introduzione al controllo del database SQL](sql-database-auditing.md).
- Per ulteriori informazioni sui programmi di Azure che supportano la conformità agli standard, vedere la [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

<!--Image references-->









