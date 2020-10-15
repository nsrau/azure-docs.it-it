---
title: Controllo delle istanze gestite di SQL
description: Informazioni su come iniziare a usare il controllo Istanza gestita SQL di Azure con T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: d8a6ead23e080b5e1e17403873e2dbaedc0ce177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620359"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Introduzione al controllo Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Il controllo [istanza gestita SQL di Azure](sql-managed-instance-paas-overview.md) tiene traccia degli eventi di database e li scrive in un log di controllo nell'account di archiviazione di Azure. Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che possono indicare problemi aziendali o possibili violazioni della sicurezza.
- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per ulteriori informazioni sui programmi di Azure che supportano la conformità agli standard, vedere la [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurare il controllo per il server in Archiviazione di Azure

La sezione seguente descrive la configurazione del controllo per l'istanza gestita.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Creare un **contenitore** di Archiviazione di Azure in cui archiviare i log di controllo.

   1. Passare all'account di archiviazione di Azure in cui si vogliono archiviare i log di controllo.

      > [!IMPORTANT]
      > - Usare un account di archiviazione nella stessa area dell'istanza gestita per evitare operazioni di lettura e scrittura tra aree diverse. 
      > - Se l'account di archiviazione si trova dietro una rete virtuale o un firewall, vedere [Concedere l'accesso da una rete virtuale](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).
      > - Se si modifica il periodo di conservazione da 0 (conservazione illimitata) a qualsiasi altro valore, tenere presente che la conservazione verrà applicata solo ai log scritti dopo la modifica del valore di conservazione (i log scritti durante il periodo in cui la conservazione è stata impostata su illimitata vengono conservati, anche dopo l'abilitazione della conservazione).

   1. Nell'account di archiviazione passare a **Panoramica** e fare clic su **BLOB**.

      ![Widget BLOB di Azure](./media/auditing-configure/1_blobs_widget.png)

   1. Nel menu in alto fare clic su **+ Contenitore** per creare un nuovo contenitore.

      ![Icona per creare un contenitore BLOB](./media/auditing-configure/2_create_container_button.png)

   1. Specificare un **nome**per il contenitore, impostare il **livello di accesso pubblico** su **privato**e quindi fare clic su **OK**.

      ![Creare una configurazione del contenitore BLOB](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > I clienti che desiderano configurare un archivio di log non modificabile per gli eventi di controllo a livello di server o di database devono seguire le [istruzioni fornite da archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes). Assicurarsi di aver selezionato **Consenti aggiunte aggiuntive** quando si configura l'archiviazione BLOB non modificabile.
  
3. Dopo aver creato il contenitore per i log di controllo, è possibile configurarlo come destinazione per i log di controllo in due modi: [usando T-SQL](#blobtsql) o [l'interfaccia utente di SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Configurare l'archiviazione BLOB per i log di controllo con T-SQL:

     1. Nell'elenco dei contenitori fare clic sul contenitore appena creato e quindi fare clic su **Proprietà del contenitore**.

        ![Pulsante delle proprietà del contenitore BLOB](./media/auditing-configure/4_container_properties_button.png)

     1. Copiare l'URL del contenitore facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro. Il formato dell'URL del contenitore deve essere `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL da copiare del contenitore BLOB](./media/auditing-configure/5_container_copy_name.png)

     1. Generare un **token** di firma di accesso condiviso di archiviazione di Azure per concedere al controllo dell'istanza gestita i diritti di accesso all'account di archiviazione:

        - Passare all'account di archiviazione di Azure in cui è stato creato il contenitore nel passaggio precedente.

        - Fare clic su **firma di accesso condiviso** nel menu **impostazioni di archiviazione** .

          ![Icona Firma di accesso condiviso nel menu Impostazioni di archiviazione](./media/auditing-configure/6_storage_settings_menu.png)

        - Configurare SAS come segue:

          - **Servizi consentiti**: BLOB

          - **Data di inizio**: per evitare problemi relativi al fuso orario, usare la data di ieri

          - **Data di fine**: scegliere la data di scadenza del token di firma di accesso condiviso

            > [!NOTE]
            > Rinnovare il token alla scadenza per evitare errori di controllo.

          - Fare clic su **Genera firma di accesso condiviso**.

            ![Configurazione della firma di accesso condiviso](./media/auditing-configure/7_sas_configure.png)

        - Il token di firma di accesso condiviso viene visualizzato in basso. Copiare il token facendo clic sull'icona di copia e salvarlo (ad esempio, in Blocco note) per uso futuro.

          ![Copiare il token di accesso condiviso](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Rimuovere il carattere punto interrogativo ("?") dall'inizio del token.

     1. Connettersi all'istanza gestita tramite SQL Server Management Studio o qualsiasi altro strumento supportato.

     1. Eseguire l'istruzione T-SQL seguente per **creare una nuova credenziale** usando l'URL del contenitore e il token di firma di accesso condiviso creati nei passaggi precedenti:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Eseguire l'istruzione T-SQL seguente per creare un nuovo controllo del server (scegliere il nome del controllo personalizzato e usare l'URL del contenitore creato nei passaggi precedenti). Se non è specificato, il `RETENTION_DAYS` valore predefinito è 0 (conservazione illimitata):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

        Continuare [creando una specifica del controllo del server o una specifica del controllo del database](#createspec).

   - <a id="blobssms"></a>Configurare l'archiviazione BLOB per i log di controllo usando SQL Server Management Studio 18 (anteprima):

     1. Connettersi all'istanza gestita usando l'interfaccia utente di SQL Server Management Studio.

     1. Espandere la nota radice del Esplora oggetti.

     1. Espandere il nodo **sicurezza** , fare clic con il pulsante destro del mouse sul nodo **controlli** e fare clic su **nuovo controllo**:

        ![Espandere i nodi Sicurezza e Controlli](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Verificare che l' **URL** sia selezionato in **destinazione controllo** e fare clic su **Sfoglia**:

        ![Sfogliare Archiviazione di Azure](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (Facoltativo) Accedere all'account di Azure:

        ![Accedere ad Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Selezionare una sottoscrizione, un account di archiviazione e un contenitore BLOB dall'elenco a discesa oppure creare un contenitore personalizzato facendo clic su **Crea**. Al termine, fare clic su **OK**:

        ![Selezionare la sottoscrizione di Azure, l'account di archiviazione e il contenitore BLOB](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Fare clic su **OK** nella finestra di dialogo **Crea controllo** .

4. <a id="createspec"></a>Dopo aver configurato il contenitore BLOB come destinazione per i log di controllo, creare e abilitare una specifica del controllo del server o una specifica del controllo del database come per SQL Server:

   - [Guida a T-SQL per la specifica del controllo del server](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guida di create database audit specification T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Abilitare il controllo server creato nel passaggio 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Per altre informazioni:

- [Controllo delle differenze tra Istanza gestita SQL di Azure e un database in SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Configurare il controllo per il server a hub eventi o ai log di monitoraggio di Azure

I log di controllo da un'istanza gestita possono essere inviati a hub eventi di Azure o ai log di monitoraggio di Azure. Questa sezione illustra come configurarli:

1. Spostarsi nella [portale di Azure](https://portal.azure.com/) all'istanza gestita.

2. Fare clic su **Impostazioni di diagnostica**.

3. Fare clic su **Attiva diagnostica**. Se la diagnostica è già abilitata, verrà invece visualizzata l' **opzione + Aggiungi diagnostica** .

4. Selezionare **SQLSecurityAuditEvents** nell'elenco dei log.

5. Selezionare una destinazione per gli eventi di controllo: Hub eventi, log di monitoraggio di Azure o entrambi. Configurare per ogni destinazione i parametri obbligatori (ad esempio Log Analytics dell'area di lavoro).

6. Fare clic su **Salva**.

    ![Configurare le impostazioni di diagnostica](./media/auditing-configure/9_mi_configure_diagnostics.png)

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

- È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). In archiviazione di Azure, i log di controllo vengono salvati come una raccolta di file BLOB all'interno di un contenitore definito per archiviare i log di controllo. Per altri dettagli sulla gerarchia della cartella di archiviazione, le convenzioni di denominazione e il formato dei log, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

- Per un elenco completo dei metodi di utilizzo del log di controllo, vedere [Introduzione al controllo del database SQL di Azure](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Utilizzare i log archiviati in hub eventi

Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la documentazione di Hub eventi di Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Utilizzare e analizzare i log archiviati nei log di monitoraggio di Azure

Se i log di controllo vengono scritti in Log di Monitoraggio di Azure, saranno disponibili nell'area di lavoro Log Analytics, in cui è possibile eseguire ricerche avanzate sui dati di controllo. Come punto di partenza, passare all'area di lavoro Log Analytics. Nella sezione **generale** fare clic su **log** e immettere una semplice query, ad esempio: `search "SQLSecurityAuditEvents"` per visualizzare i log di controllo.  

Log di Monitoraggio di Azure consente di ottenere informazioni operative in tempo reale tramite funzionalità di ricerca integrate e dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca e i comandi di Log di Monitoraggio di Azure, vedere [Guida di riferimento alla ricerca in Log di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Controllo delle differenze tra i database in Istanza gestita SQL di Azure e i database in SQL Server

Le differenze principali tra il controllo nei database in Azure SQL Istanza gestita e i database in SQL Server sono:

- Con Istanza gestita SQL di Azure, il controllo funziona a livello di server e archivia `.xel` i file di log nell'archivio BLOB di Azure.
- In SQL Server, il controllo funziona a livello di server, ma archivia gli eventi nei registri eventi di sistema o di Windows.

Il controllo XEvent nelle istanze gestite supporta le destinazioni di archiviazione BLOB di Azure. I log di file e di Windows **non sono supportati**.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- Viene fornita una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui `.xel` vengono inseriti i file.
- Viene fornita una nuova sintassi `TO EXTERNAL MONITOR` per abilitare gli hub eventi e le destinazioni del log di monitoraggio di Azure.
- La sintassi `TO FILE` **non è supportata** perché SQL istanza gestita di Azure non è in grado di accedere alle condivisioni file di Windows.
- L'opzione Shutdown **non è supportata**.
- Un valore di `queue_delay` uguale a 0 **non è supportato**.

## <a name="next-steps"></a>Passaggi successivi

- Per un elenco completo dei metodi di utilizzo del log di controllo, vedere [Introduzione al controllo del database SQL di Azure](../../azure-sql/database/auditing-overview.md).
- Per ulteriori informazioni sui programmi di Azure che supportano la conformità agli standard, vedere la [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità.

<!--Image references-->
