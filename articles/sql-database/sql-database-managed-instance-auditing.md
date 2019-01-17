---
title: Controllo di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come iniziare a eseguire il controllo di Istanza gestita di database SQL di Azure usando T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: vainolo
ms.reviewer: vanto
manager: craigg
ms.date: 01/12/2019
ms.openlocfilehash: 716c4caa1b28cc40470d366e5fc6901de9462f9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267267"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introduzione al controllo di Istanza gestita di database SQL di Azure

Il controllo di [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md) tiene traccia degli eventi del database e li registra in un log di controllo nell'account di archiviazione di Azure dell'utente. Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.
- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurare il controllo per il server in Archiviazione di Azure 

La sezione seguente descrive la configurazione del controllo per Istanza gestita.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. La procedura seguente crea il **contenitore** di Archiviazione di Azure in cui archiviare i log di controllo.

   - Passare all'archiviazione di Azure in cui si vogliono archiviare i log di controllo.

     > [!IMPORTANT]
     > Usare un account di archiviazione nella stessa area del server di Istanza gestita per evitare operazioni di lettura e scrittura tra aree diverse.

   - Nell'account di archiviazione passare a **Panoramica** e fare clic su **BLOB**.

     ![Riquadro di spostamento][1]

   - Nel menu in alto fare clic su **+ Contenitore** per creare un nuovo contenitore.

     ![Riquadro di spostamento][2]

   - Specificare un **Nome** per il contenitore, impostare Livello di accesso pubblico su **Privato** e quindi fare clic su **OK**.

     ![Riquadro di spostamento][3]

   - Nell'elenco dei contenitori fare clic sul contenitore appena creato e quindi fare clic su **Proprietà del contenitore**.

     ![Riquadro di spostamento][4]

   - Copiare l'URL del contenitore facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro. Il formato dell'URL del contenitore deve essere `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Riquadro di spostamento][5]

3. La procedura seguente genera un **token di firma di accesso condiviso** di Archiviazione di Azure usato per concedere al controllo di Istanza gestita diritti di accesso all'account di archiviazione.

   - Passare all'account di Archiviazione di Azure in cui è stato creato il contenitore nel passaggio precedente.

   - Fare clic su **Firma di accesso condiviso** nel menu Impostazioni di archiviazione.

     ![Riquadro di spostamento][6]

   - Configurare SAS come segue:
     - **Servizi consentiti**: BLOB
     - **Data di inizio**: per evitare problemi correlati al fuso orario, è consigliabile usare la data del giorno precedente.
     - **Data di fine**: scegliere la data di scadenza del token di firma di accesso condiviso. 

       > [!NOTE]
       > Rinnovare il token alla scadenza per evitare errori di controllo.

     - Fare clic su **Genera firma di accesso condiviso**.

       ![Riquadro di spostamento][7]

   - Fare clic su Genera firma di accesso condiviso. Il token verrà visualizzato in basso. Copiare il token facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro.

     > [!IMPORTANT]
     > Rimuovere il carattere punto interrogativo ("?") dall'inizio del token.

     ![Riquadro di spostamento][8]

4. Connettersi a Istanza gestita tramite SQL Server Management Studio (SSMS).

5. Eseguire l'istruzione T-SQL seguente per **creare nuove credenziali** usando l'URL del contenitore e il token di firma di accesso condiviso creati nei passaggi precedenti:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Eseguire l'istruzione T-SQL seguente per creare un nuovo controllo server. Scegliere un nome personalizzato per il controllo, usare l'URL del contenitore creato nei passaggi precedenti:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Se non specificato altrimenti, l'impostazione predefinita di `RETENTION_DAYS` è 0 (conservazione illimitata).

    Per altre informazioni:
    - [Controllo delle differenze tra Istanza gestita, database SQL di Azure e SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Creare una specifica di controllo server o di controllo database come si farebbe per SQL Server:
    - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Abilitare il controllo server creato nel passaggio 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="set-up-auditing-for-your-server-to-event-hub-or-log-analytics"></a>Configurare il controllo per il server in Hub eventi o Log Analytics

I log di controllo da un'istanza gestita possono essere inviati anche ad hub eventi o Log Analytics tramite Monitoraggio di Azure. Questa sezione illustra come configurarli:

1. Spostarsi all'interno di [portale di Azure](https://portal.azure.com/) in Istanza gestita di database SQL.

2. Fare clic su **Impostazioni di diagnostica**.

3. Fare clic su **Attiva diagnostica**. Se la diagnostica è già abilitata verrà invece visualizzata l'opzione *+ Aggiungi impostazione di diagnostica*.

4. Selezionare **SQLSecurityAuditEvents** nell'elenco dei log.

5. Selezionare una destinazione per gli eventi di controllo: Hub eventi, Log Analytics o entrambi. Configurare per ogni destinazione i parametri obbligatori (ad esempio Log Analytics dell'area di lavoro).

6. Fare clic su **Save**.

  ![Riquadro di spostamento][9]

7. Connettersi all'istanza gestita usando **SQL Server Management Studio (SSMS)** o qualsiasi altro client supportato.

8. Eseguire l'istruzione T-SQL seguente per creare un controllo del server:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Creare una specifica di controllo server o di controllo database come si farebbe per SQL Server:

   - [Guida di T-SQL per la creazione di specifiche di controllo server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di T-SQL per la creazione di specifiche di controllo database](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Abilitare il controllo server creato nel passaggio 7:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Utilizzo di log di controllo

### <a name="consume-logs-stored-in-azure-storage"></a>Utilizzo dei log archiviati in Archiviazione di Azure

Per visualizzare i log di controllo dei BLOB sono disponibili diversi metodi.

- Usare la funzione di sistema `sys.fn_get_audit_file` (T-SQL) per restituire i dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere la [documentazione su sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/). Nell'archiviazione di Azure, i log del controllo vengono salvati come raccolta di file BLOB in un contenitore denominato sqldbauditlogs. Per altri dettagli sulla gerarchia della cartella di archiviazione, le convenzioni di denominazione e il formato dei log, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

- Per l'elenco completo dei metodi di consumo del log di controllo, fare riferimento a [Introduzione al controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Il metodo per la visualizzazione dei record di controllo dal portale di Azure (riquadro "Record di controllo") non è attualmente disponibile per Istanza gestita.

### <a name="consume-logs-stored-in-event-hub"></a>Utilizzo dei log archiviati in Hub eventi

Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la documentazione di Hub eventi di Azure.

### <a name="consume-and-analyze-logs-stored-in-log-analytics"></a>Usare e analizzare i log archiviati in Log Analytics

Se i log di controllo vengono scritti in Log Analytics, saranno disponibili nell'area di lavoro di Log Analytics, in cui è possibile eseguire ricerche avanzate sui dati di controllo. Come punto di partenza, passare a Log Analytics e nella sezione *Generali* fare clic su *Log* e immettere una query semplice, ad esempio: `search "SQLSecurityAuditEvents"` per visualizzare i log di controllo.  

Log Analytics consente di ottenere informazioni operative in tempo reale tramite funzionalità di ricerca integrate e dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca e i comandi di Log Analytics, vedere [Guida di riferimento alla ricerca in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Controllo delle differenze tra Istanza gestita, database SQL di Azure e SQL Server

Le principali differenze tra il controllo SQL in Istanza gestita, il database SQL di Azure e SQL Server locale sono le seguenti:

- In Istanza gestita il controllo SQL viene eseguito a livello del server e archivia file di log con estensione `.xel` nell'account di archiviazione BLOB di Azure.
- Nel database SQL di Azure il controllo SQL lavora a livello del database.
- In SQL Server locale o in macchine virtuali SQL Server il controllo SQL funziona a livello del server, ma memorizza gli eventi nei log eventi del file system o di Windows.

Il controllo XEvent in Istanza gestita supporta le destinazioni di archiviazione BLOB di Azure. I log di file e di Windows **non sono supportati**.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo nell'archivio BLOB di Azure sono le seguenti:

- È disponibile una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui vengono inseriti i file con estensione `.xel`.
- Viene fornita una nuova sintassi `TO EXTERNAL MONITOR` per abilitare i target Hub eventi e Log Analytics.
- La sintassi `TO FILE` **non è supportata** perché Istanza gestita non può accedere alle condivisioni file di Windows.
- L'opzione Shutdown **non è supportata**.
- Un valore di `queue_delay` uguale a 0 **non è supportato**.

## <a name="next-steps"></a>Passaggi successivi

- Per l'elenco completo dei metodi di consumo del log di controllo, fare riferimento a [Introduzione al controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).
- Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
[9]: ./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png
