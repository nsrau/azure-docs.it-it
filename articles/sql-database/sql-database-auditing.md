---
title: Introduzione al controllo del database SQL di Azure | Microsoft Docs
description: Usare il controllo del database SQL di Azure per tenere traccia degli eventi del database in un log di controllo.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 04/16/2019
ms.openlocfilehash: 69fe3287083523a3a47975a3db51d7241681f5c4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68569503"
---
# <a name="get-started-with-sql-database-auditing"></a>Introduzione al controllo del database SQL

Il controllo del [database SQL di Azure](sql-database-technical-overview.md) e di [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) consente di tenere traccia degli eventi che si verificano nel database e registrarli in un log di controllo nell'account di Archiviazione di Azure, nell'area di lavoro di OMS o in Hub eventi. Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.

- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per ulteriori informazioni sui programmi di Azure che supportano la conformità agli standard, vedere la [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.


> [!NOTE] 
> Questo argomento è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Panoramica del servizio di controllo del database SQL di Azure

È possibile usare il servizio di controllo del database SQL per eseguire le operazioni seguenti:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l'attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

> [!IMPORTANT]
> I log di controllo vengono scritti in **BLOB di accodamento** nella risorsa di archiviazione BLOB di Azure della sottoscrizione di Azure.
>
> - Sono supportati tutti i tipi di archiviazione (v1, v2, BLOB).
> - Sono supportate tutte le configurazioni di replica di archiviazione.
> - **Archiviazione Premium** attualmente **non è supportata**.
> - **Storage in VNet** attualmente **non è supportato**.
> - L'**archiviazione protetta da firewall** attualmente **non è supportata**

## <a id="subheading-8"></a>Definire criteri di controllo a livello di server o a livello di database

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti:

- I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

- Se *il controllo BLOB del server è abilitato*, *si applica sempre al database*. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

- L'abilitazione del controllo BLOB nel database o nel data warehouse in aggiunta all'abilitazione nel server *non* sostituisce né modifica le impostazioni del controllo BLOB del server. I due controlli coesisteranno. In altre parole, il database viene controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

   > [!NOTE]
   > È consigliabile evitare di abilitare contemporaneamente il controllo BLOB del server e il controllo BLOB del database ad eccezione dei casi seguenti:
    > - Si vuole usare un *account di archiviazione* o un *periodo di conservazione* diverso per un database specifico.
    > - Per un database specifico si vogliono controllare tipi o categorie di eventi diversi dagli altri database nel server. Ad esempio, potrebbe essere necessario controllare gli inserimenti di tabella solo per un database specifico.
   >
   > Negli altri casi, è consigliabile abilitare solo il controllo BLOB a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.

## <a id="subheading-2"></a>Configurare il controllo per il database

Nella sezione seguente è descritta la configurazione del controllo mediante il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Controllo** nell'intestazione Sicurezza nel riquadro del database SQL o del server.

    <a id="auditing-screenshot"></a>![Riquadro di spostamento][1]

3. Se si preferisce configurare un criterio di controllo del server, è possibile selezionare il collegamento **Visualizza impostazioni del server** nella pagina relativa al controllo del database. Si possono quindi visualizzare o modificare le impostazioni di controllo del server. I criteri di controllo del server si applicano a tutti i database esistenti e ai nuovi database creati in questo server.

    ![Riquadro di spostamento][2]

4. Se si preferisce abilitare il controllo a livello di database, impostare **Controllo** su **ATTIVA**.

    Se il controllo del server è abilitato, il controllo configurato del database coesisterà con il controllo del server.

    ![Riquadro di spostamento][3]

5. **Nuovo** -sono ora disponibili più opzioni per la configurazione in cui verranno scritti i log di controllo. È possibile scrivere i log in un account di archiviazione di Azure, in un'area di lavoro Log Analytics per l'uso da log di monitoraggio di Azure o nell'hub eventi per l'uso con hub eventi. È possibile configurare qualsiasi combinazione di queste opzioni e verranno scritti i log di controllo per ognuno.

   > [!WARNING]
   > L'abilitazione del controllo per Log Analytics comporterà costi in base ai tassi di inserimento. Per conoscere i costi associati, usare questa [opzione](https://azure.microsoft.com/pricing/details/monitor/)oppure prendere in considerazione l'archiviazione dei log di controllo in un account di archiviazione di Azure.

    ![opzioni di archiviazione](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Per configurare la scrittura dei log per un account di archiviazione, selezionare **memorizzazione** e aprire **dettagli archiviazione**. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e quindi selezionare il periodo di conservazione. I log meno recenti verranno eliminati. Fare quindi clic su **OK**.

    ![archiviazione di Azure](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Per configurare la scrittura dei log di controllo in un'area di lavoro Log Analytics, selezionare **Log Analytics (anteprima)** e aprire **Dettagli di Log Analytics**. Selezionare o creare l'area di lavoro Log Analytics in cui verranno scritti i log e quindi scegliere **OK**.

    ![Area di lavoro Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Per configurare la scrittura dei log a un hub eventi, selezionare **Hub eventi (anteprima)** e aprire **i dettagli dell'Hub eventi**. Selezionare l'hub eventi in cui verranno scritti i log e quindi fare clic su **OK**. Assicurarsi che l'hub eventi si trovi nella stessa area del database e server.

    ![Hub eventi](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Fare clic su **Save**.
10. Per personalizzare gli eventi controllati, è possibile usare i [cmdlet PowerShell](#subheading-7) o l'[API REST](#subheading-9).
11. Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità di rilevamento delle minacce e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. Quando si usa il rilevamento delle minacce, si ricevono avvisi proattivi sulle attività di database anomale che possono indicare potenziali minacce per la sicurezza. Per altre informazioni, vedere [Introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Non è possibile abilitare il controllo in una Azure SQL Data Warehouse sospesa. Per abilitarla, annullare la sospensione dell'data warehouse.

> [!WARNING]
> L'abilitazione del controllo in un server che dispone di un Azure SQL Data Warehouse su di essa comporterà **la ripresa della data warehouse e la nuova sospensione, il** che potrebbe comportare addebiti per la fatturazione.

## <a id="subheading-3"></a>Analizzare i log di controllo e i report

Se si sceglie di scrivere i log di controllo nei log di monitoraggio di Azure:

- Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore della pagina **Controllo** del database fare clic su **Visualizza log di controllo**.

    ![visualizzare i log di controllo](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Quindi, facendo clic su **aprire in OMS** nella parte superiore della pagina **record di controllo** verrà aperta la visualizzazione dei log in Log Analytics, in cui è possibile personalizzare l'intervallo di tempo e la query di ricerca.

    ![aprire in Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- In alternativa, è possibile accedere anche i log di controllo dal pannello Log Analytics. Aprire l'area di lavoro Log Analytics e nella sezione **generale** fare clic su **log**. È possibile iniziare con una query semplice, ad esempio: *cercare "SQLSecurityAuditEvents"* per visualizzare i log di controllo.
    Da qui è anche possibile usare i [log di monitoraggio di Azure](../log-analytics/log-analytics-log-search.md) per eseguire ricerche avanzate sui dati del log di controllo. Log di monitoraggio di Azure offre informazioni operative in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca dei log di Azure e sui comandi, vedere i [riferimenti alla ricerca nei log di monitoraggio](../log-analytics/log-analytics-log-search.md)di Azure.

Se si sceglie di scrivere i log di controllo su Hub eventi:

- Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/).
- I log di controllo in Hub eventi vengono acquisiti nel corpo degli eventi di [Apache Avro](https://avro.apache.org/) e archiviati usando la formattazione JSON con codifica UTF-8. Per leggere i log di controllo, è possibile usare [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) o strumenti simili in grado di elaborare tale formato.

Se si sceglie di scrivere i log di controllo in un account di archiviazione di Azure, esistono diversi metodi che è possibile usare per visualizzare i log:

- I log di controllo vengono aggregati nell'account selezionato durante la configurazione. È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](https://storageexplorer.com/). Nell'archiviazione di Azure, i log del controllo vengono salvati come raccolta di file BLOB in un contenitore denominato **sqldbauditlogs**. Per ulteriori informazioni sulla gerarchia della cartella di archiviazione, le convenzioni di denominazione e il formato di log, vedere il [formato del registro di controllo del database SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore della pagina **Controllo** del database fare clic su **Visualizza log di controllo**.

    ![Riquadro di spostamento][7]

    Verrà aperto **Record di controllo**, da cui sarà possibile visualizzare i log.

  - È possibile visualizzare date specifiche facendo clic su **Filtro** nella parte superiore della pagina **Record di controllo**.
  - È possibile passare da un record di controllo all'altro creato dai *criteri di controllo del server*  e dai *criteri di controllo del database* attivando o disattivando **Origine controllo**.
  - È possibile visualizzare solo i record di controllo correlati a SQL injection selezionando la casella di controllo **Visualizza solo i record di controllo per SQL injection**.

       ![Riquadro di spostamento][8]

- Usare la funzione di sistema **sys.fn_get_audit_file** (T-SQL) per tornare ai dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Usare **Unisci file di controllo** in SQL Server Management Studio (a partire da SSMS 17):
    1. Dalla barra dei menu di SSMS selezionare **File** > **Apri** > **Unisci file di controllo**.

        ![Riquadro di spostamento][9]
    2. Verrà visualizzata la finestra di dialogo **Aggiunti file di controllo**. Selezionare una delle opzioni **Aggiungi** per scegliere se unire i file di controllo da un disco locale oppure importarli da Archiviazione di Azure. È necessario specificare i dettagli e la chiave dell'account di Archiviazione di Azure.

    3. Dopo aver aggiunto tutti i file da unire, fare clic su **OK** per completare l'operazione di unione.

    4. Il file unito verrà aperto in SSMS, dove potrà essere visualizzato, analizzato ed esportato in un file XEL o CSV o in una tabella.

- Usare Power BI. È possibile visualizzare e analizzare i dati dei log di controllo in Power BI. Per altre informazioni e per accedere a un modello scaricabile, vedere [Analyze audit log data in Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/) (Analizzare i dati di log di controllo in Power BI).
- Scaricare i file di log dal contenitore BLOB del servizio di archiviazione di Azure tramite il portale o con uno strumento come [Azure Storage Explorer](https://storageexplorer.com/).
  - Dopo aver scaricato un file di log in locale, fare doppio clic sul file per aprire, visualizzare e analizzare i log in SSMS.
  - È anche possibile scaricare più file contemporaneamente tramite Azure Storage Explorer. Per farlo, fare clic con il pulsante destro del mouse su una sottocartella specifica e scegliere **Salva con nome** per salvarla in una cartella locale.

- Altri metodi:

  - Dopo avere scaricato diversi file o una sottocartella contenente i file di log, è possibile unirli in locale come descritto nelle istruzioni relative all'unione di file di controllo in SSMS riportate in precedenza.
  - Visualizzare i log del controllo BLOB a livello di codice:

    - [Eseguire query sui file di eventi estesi](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) con PowerShell.

## <a id="subheading-5"></a>Procedure nell'ambiente di produzione

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Controllo dei database con replica geografica</a>

Con i database con replica geografica, quando si abilita il controllo nel database primario il database secondario disporrà di un criterio di controllo identico. È anche possibile impostare il controllo nel database secondario abilitando il controllo nel **server secondario**, in modo indipendente dal database primario.

- Livello server (**consigliato**): attivare il controllo sia nel **server primario** che nel **server secondario**. I database primari e secondari saranno controllati in modo indipendente in base ai rispettivi criteri a livello di server.
- A livello di database: il controllo a livello di database per i database secondari può essere configurato solo mediante le impostazioni di controllo del database primario.
  - Il controllo deve essere abilitato nello *stesso database primario* e non nel server.
  - Dopo che il controllo è stato abilitato nel database primario, verrà abilitato anche nel database secondario.

    >[!IMPORTANT]
    >In caso di controllo a livello di database, le impostazioni di archiviazione per il database secondario sono identiche a quelle del database primario, e causano traffico tra le aree. È consigliabile abilitare solo il controllo a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.
    > [!WARNING]
    > L'uso di hub eventi o di log di monitoraggio di Azure come destinazioni per i log di controllo a livello di server non è attualmente supportato per i database secondari con replica geografica.

### <a id="subheading-6">Rigenerazione delle chiavi di archiviazione</a>

Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si scrivono i log di controllo nell'archiviazione di Azure, è necessario salvare nuovamente i criteri di controllo quando si aggiornano le chiavi. Il processo è il seguente:

1. Aprire **Dettagli archiviazione**. Nella casella **Chiave di accesso alle risorse di archiviazione** selezionare **Secondaria** e fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.

    ![Riquadro di spostamento][5]
2. Passare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso primaria.

    ![Riquadro di spostamento][6]
3. Tornare alla pagina di configurazione del controllo, modificare la chiave di accesso alle risorse di archiviazione da secondaria a primaria e quindi fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.
4. Tornare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso secondaria (in preparazione al successivo ciclo di aggiornamento della chiave).

## <a name="additional-information"></a>Informazioni aggiuntive

- Per dettagli sul formato dei log, sulla gerarchia della cartella di archiviazione e sulle convenzioni di denominazione, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Il servizio di controllo del database SQL di Azure archivia 4000 caratteri di dati per i campi di tipo carattere in un record di controllo. Quando i valori **statement** o **data_sensitivity_information** restituiti da un'azione controllabile contengono più di 4000 caratteri, i dati oltre i primi 4000 caratteri verranno **troncati e non controllati**.

- I log di controllo vengono scritti in **Accodare BLOB**nell'archivio BLOB di Azure della sottoscrizione di Azure:
  - **Archiviazione Premium** attualmente **non è supportata** da Accodare BLOB.
  - **Storage in VNet** attualmente **non è supportato**.

- I criteri di controllo predefinito includono tutte le azioni e il set di gruppi di azioni seguente, che controllerà tutte le query e le stored procedure eseguite sul database, nonché gli accessi riusciti e non riusciti:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    È possibile configurare il controllo per diversi tipi di azioni e gruppi di azioni tramite PowerShell, come descritto nella sezione [Gestire il controllo del database SQL usando Azure PowerShell](#subheading-7).

- Quando si usa l'autenticazione di AAD, i record degli accessi non riusciti *non* vengono visualizzati nel log di controllo di SQL. Per visualizzare i record di controllo degli accessi non riusciti, è necessario visitare il [portale di Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), che registra i dettagli di questi eventi.


## <a id="subheading-7"></a>Gestire il controllo del database SQL usando Azure PowerShell

**Cmdlet PowerShell (incluso il supporto della clausola WHERE per altri filtri)** :

- [Crea o aggiorna i criteri di controllo del database (set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Crea o aggiorna i criteri di controllo del server (set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Ottenere i criteri di controllo del database (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Ottenere i criteri di controllo del server (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Rimuovere i criteri di controllo del database (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Rimuovere i criteri di controllo del server (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Gestire il controllo del database SQL usando l'API REST

**API REST**:

- [Creare o aggiornare i criteri controllo del database](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Creare o aggiornare i criteri controllo del server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i criteri controllo del database](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Ottenere i criteri controllo del server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Criteri estesi con il supporto della clausola WHERE per altri filtri:

- [Crea o aggiorna i criteri di controllo *esteso* del database](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Crea o aggiorna i criteri di controllo *esteso* del server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i criteri di controllo *esteso* del database](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Ottenere i criteri di controllo estesi del server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Gestire il controllo del database SQL usando i modelli ARM

È possibile gestire il controllo del database SQL di Azure usando i modelli di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), come illustrato negli esempi seguenti:

- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in un account di Archiviazione BLOB di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Gli esempi collegati si trovano in un repository pubblico esterno e vengono forniti "così come sono", senza garanzia, e non sono supportati in alcun programma o servizio di supporto tecnico Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
