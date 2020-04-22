---
title: Controllo di Azure SQL
description: Usare il controllo del database SQL di Azure per tenere traccia degli eventi del database in un log di controllo.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 9e8aa9bbbdf166ba0caf29cd0bce22b8ed321e4e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685196"
---
# <a name="azure-sql-auditing"></a>Controllo di Azure SQL

Il controllo per [il database SQL di](sql-database-technical-overview.md) Azure e Analisi [synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) di Azure tiene traccia degli eventi del database e li scrive in un log di controllo nell'account di archiviazione di Azure, nell'area di lavoro log di Log Analytics o negli hub eventi. 

Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.

- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) di Azure in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

> [!NOTE] 
> Questo argomento si applica sia al database SQL di Azure che ai database di Azure Synapse Analytics.This topic applies to both Azure SQL Database, and Azure Synapse Analytics databases. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL di Azure che all'analisi di Azure Synapse.For simplicity, SQL Database is used when referring to both Azure SQL Database and Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Panoramica

È possibile usare il servizio di controllo del database SQL per eseguire le operazioni seguenti:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l'attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

> [!IMPORTANT]
> - Il controllo del database SQL di Azure è ottimizzato per la disponibilità & le prestazioni. Durante l'attività molto elevata, il database SQL di Azure consente alle operazioni di procedere e potrebbe non registrare alcuni eventi controllati.

#### <a name="auditing-limitations"></a>Limitazioni del controllo

- **Archiviazione Premium** attualmente **non è supportata**.
- **Lo spazio dei nomi gerarchico per l'account** di **archiviazione Gen2** di Archiviazione dati di Azure non è attualmente **supportato.**
- L'abilitazione del controllo in un **data warehouse SQL** di Azure in pausa non è supportata. Per abilitare il controllo, riprendere il data warehouse.

## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definire criteri di controllo a livello di server o a livello di database

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti:

- I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

- Se *il controllo BLOB del server è abilitato*, *si applica sempre al database*. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

- L'abilitazione del controllo BLOB nel database o nel data warehouse in aggiunta all'abilitazione nel server *non* sostituisce né modifica le impostazioni del controllo BLOB del server. I due controlli coesisteranno. In altre parole, il database viene controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

   > [!NOTE]
   > È consigliabile evitare di abilitare contemporaneamente il controllo BLOB del server e il controllo BLOB del database ad eccezione dei casi seguenti:
    > - Si vuole usare un *account di archiviazione,* un periodo di *conservazione* o un'area di lavoro di *Log Analytics* per un database specifico.
    > - Per un database specifico si vogliono controllare tipi o categorie di eventi diversi dagli altri database nel server. Ad esempio, potrebbe essere necessario controllare gli inserimenti di tabella solo per un database specifico.
   >
   > Negli altri casi, è consigliabile abilitare solo il controllo BLOB a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurare il controllo per il server

I criteri di controllo predefinito includono tutte le azioni e il set di gruppi di azioni seguente, che controllerà tutte le query e le stored procedure eseguite sul database, nonché gli accessi riusciti e non riusciti:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
È possibile configurare il controllo per diversi tipi di azioni e gruppi di azioni tramite PowerShell, come descritto nella sezione [Gestire il controllo del database SQL usando Azure PowerShell](#manage-auditing).

Il servizio di controllo del database SQL di Azure archivia 4000 caratteri di dati per i campi di tipo carattere in un record di controllo. Quando i valori **statement** o **data_sensitivity_information** restituiti da un'azione controllabile contengono più di 4000 caratteri, i dati oltre i primi 4000 caratteri verranno **troncati e non controllati**.
Nella sezione seguente è descritta la configurazione del controllo mediante il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Controllo** nell'intestazione Sicurezza nel riquadro del database SQL o del server.
3. Se si preferisce configurare un criterio di controllo del server, è possibile selezionare il collegamento **Visualizza impostazioni del server** nella pagina relativa al controllo del database. Si possono quindi visualizzare o modificare le impostazioni di controllo del server. I criteri di controllo del server si applicano a tutti i database esistenti e ai nuovi database creati in questo server.

    ![Riquadro di spostamento](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Se si preferisce abilitare il controllo a livello di database, impostare **Controllo** su **ATTIVA**. Se il controllo del server è abilitato, il controllo configurato del database coesisterà con il controllo del server.

5. Sono disponibili più opzioni per la configurazione della posizione in cui verranno scritti i log di controllo. È possibile scrivere log in un account di archiviazione di Azure, in un'area di lavoro di Log Analytics per l'utilizzo da parte dei log di Monitoraggio di Azure (anteprima) o nell'hub eventi per l'utilizzo tramite l'hub eventi (anteprima). È possibile configurare qualsiasi combinazione di queste opzioni e verranno scritti i log di controllo per ognuno.
  
   ![opzioni di archiviazione](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name=""></a><a id="audit-storage-destination">Controllo alla destinazione di archiviazioneAudit to storage destination</a>

Per configurare la scrittura dei log per un account di archiviazione, selezionare **memorizzazione** e aprire **dettagli archiviazione**. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e quindi selezionare il periodo di conservazione. Fare quindi clic su **OK**. I registri precedenti al periodo di conservazione vengono eliminati.

- Il valore predefinito per il periodo di conservazione è 0 (conservazione illimitata). È possibile modificare questo valore spostando il dispositivo di scorrimento **Conservazione (giorni)** nelle **impostazioni di archiviazione** durante la configurazione dell'account di archiviazione per il controllo.
  - Se si modifica il periodo di conservazione da 0 (conservazione illimitata) a qualsiasi altro valore, si noti che la conservazione verrà applicata solo ai log scritti dopo la modifica del valore di conservazione (i registri scritti durante il periodo in cui la conservazione è stata impostata su illimitata vengono mantenuti, anche dopo l'abilitazione della conservazione).

  ![archiviazione di Azure](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Osservazioni

- I log di controllo vengono scritti in Aggiungi BLOB in un'archiviazione BLOB di Azure nella sottoscrizione di AzureAudit logs are written to **Append Blobs** in an Azure Blob storage on your Azure subscription
- Per configurare un archivio log non modificabile per gli eventi di controllo a livello di server o di database, seguire le [istruzioni fornite da Archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) di Azure (assicurarsi di aver selezionato Consenti **aggiunte aggiuntive** quando si configura l'archiviazione BLOB non modificabile).
- È possibile scrivere log di controllo in un account di Archiviazione di Azure dietro una rete virtuale o un firewall. Per istruzioni specifiche, vedere Scrivere il controllo in [un account di archiviazione dietro VNet e firewall](create-auditing-storage-account-vnet-firewall.md).
- Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità di rilevamento delle minacce e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. Quando si usa il rilevamento delle minacce, si ricevono avvisi proattivi sulle attività di database anomale che possono indicare potenziali minacce per la sicurezza. Per altre informazioni, vedere [Introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md).
- Per dettagli sul formato dei log, sulla gerarchia della cartella di archiviazione e sulle convenzioni di denominazione, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).
- Quando si usa l'autenticazione di AAD, i record degli accessi non riusciti *non* vengono visualizzati nel log di controllo di SQL. Per visualizzare i record di controllo degli accessi non riusciti, è necessario visitare il [portale di Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), che registra i dettagli di questi eventi.
- Il controllo nelle [repliche di sola lettura](sql-database-read-scale-out.md) viene abilitato automaticamente. Per ulteriori informazioni sulla gerarchia delle cartelle di archiviazione, sulle convenzioni di denominazione e sul formato del log, vedere Il formato del log di controllo del [database SQL](sql-database-audit-log-format.md). 

### <a name=""></a><a id="audit-log-analytics-destination">Controllare la destinazione di Log Analytics</a>
  
Per configurare la scrittura dei log di controllo in un'area di lavoro Log Analytics, selezionare **Log Analytics (anteprima)** e aprire **Dettagli di Log Analytics**. Selezionare o creare l'area di lavoro Log Analytics in cui verranno scritti i log e quindi scegliere **OK**.
   
   ![Area di lavoro di LogAnalytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name=""></a><a id="audit-event-hub-destination">Controllo alla destinazione dell'hub eventi</a>

> [!WARNING]
> L'abilitazione del controllo in un server in cui è presente un pool SQL **comporta la ripresa e la sospensione del pool SQL,** che potrebbe comportare costi di fatturazione.
> Impossibile abilitare il controllo in un pool SQL in pausa. Per abilitarlo, annullare la sospensione del pool SQL.

Per configurare la scrittura dei log a un hub eventi, selezionare **Hub eventi (anteprima)** e aprire **i dettagli dell'Hub eventi**. Selezionare l'hub eventi in cui verranno scritti i log e quindi fare clic su **OK**. Assicurarsi che l'hub eventi si trovi nella stessa area del database e server.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizzare i log di controllo e i report

Se si è scelto di scrivere log di controllo nei log di Monitoraggio di Azure:If you chose to write audit logs to Azure Monitor logs:

- Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore della pagina **Controllo** del database fare clic su **Visualizza log di controllo**.

    ![visualizzare i log di controllo](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Quindi, si dispone di due modi per visualizzare i registri:
    
    Facendo clic su **Log Analytics** nella parte superiore della pagina **Record** di controllo verrà aperta la visualizzazione Registri nell'area di lavoro Log Analytics, in cui è possibile personalizzare l'intervallo di tempo e la query di ricerca.
    
    ![aprire nell'area di lavoro di Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Facendo clic su **Visualizza dashboard** nella parte superiore della pagina **Record** di controllo verrà aperto un dashboard che visualizza le informazioni dei log di controllo, in cui è possibile eseguire il drill-down in Informazioni dettagliate sulla sicurezza, Accesso ai dati sensibili e altro ancora. Questo dashboard è progettato per aiutarti a ottenere informazioni dettagliate sulla sicurezza per i tuoi dati.
    È inoltre possibile personalizzare l'intervallo di tempo e la query di ricerca. 
    ![Visualizzare il dashboard di Log Analytics](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics Dashboard](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- In alternativa, è possibile accedere anche i log di controllo dal pannello Log Analytics. Aprire l'area di lavoro Log Analytics e nella sezione **generale** fare clic su **log**. È possibile iniziare con una query semplice, ad esempio: *cercare "SQLSecurityAuditEvents"* per visualizzare i log di controllo.
    Da qui è anche possibile usare [i log](../log-analytics/log-analytics-log-search.md) di Monitoraggio di Azure per eseguire ricerche avanzate nei dati del log di controllo. I log di Monitoraggio di Azure offrono informazioni operative in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare facilmente milioni di record in tutti i carichi di lavoro e nei server. Per altre informazioni utili sul linguaggio e sui comandi dei log di Monitoraggio di Azure, vedere Informazioni di riferimento sulla ricerca dei log di Monitoraggio di Azure.For additional useful information about Azure Monitor logs search language and commands, see [Azure Monitor logs search reference](../log-analytics/log-analytics-log-search.md).

Se si sceglie di scrivere i log di controllo su Hub eventi:

- Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../event-hubs/index.yml).
- I log di controllo in Hub eventi vengono acquisiti nel corpo degli eventi di [Apache Avro](https://avro.apache.org/) e archiviati usando la formattazione JSON con codifica UTF-8. Per leggere i log di controllo, è possibile usare [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) o strumenti simili in grado di elaborare tale formato.

Se si sceglie di scrivere i log di controllo in un account di archiviazione di Azure, esistono diversi metodi che è possibile usare per visualizzare i log:

- I log di controllo vengono aggregati nell'account selezionato durante la configurazione. È possibile esplorare i log di controllo usando uno strumento come [Azure Storage Explorer.You](https://storageexplorer.com/)can explore audit logs by using a tool such as Azure Storage Explorer . Nell'archiviazione di Azure, i log del controllo vengono salvati come raccolta di file BLOB in un contenitore denominato **sqldbauditlogs**. Per ulteriori informazioni sulla gerarchia delle cartelle di archiviazione, sulle convenzioni di denominazione e sul formato del log, vedere Il formato del log di controllo del [database SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore della pagina **Controllo** del database fare clic su **Visualizza log di controllo**.

    ![Riquadro di spostamento](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    Verrà aperto **Record di controllo**, da cui sarà possibile visualizzare i log.

  - È possibile visualizzare date specifiche facendo clic su **Filtro** nella parte superiore della pagina **Record di controllo**.
  - È possibile passare da un record di controllo all'altro creato dai *criteri di controllo del server * e dai *criteri di controllo del database* attivando o disattivando **Origine controllo**.
  - È possibile visualizzare solo i record di controllo correlati a SQL injection selezionando la casella di controllo **Visualizza solo i record di controllo per SQL injection**.

       ![Riquadro di spostamento]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Usare la funzione di sistema **sys.fn_get_audit_file** (T-SQL) per tornare ai dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Usare **Unisci file di controllo** in SQL Server Management Studio (a partire da SSMS 17):
    1. Scegliere **File** > **Apri** > file di**controllo file**di controllo unione dal menu SSMS .

        ![Riquadro di spostamento](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Verrà visualizzata la finestra di dialogo **Aggiunti file di controllo**. Selezionare una delle opzioni **Aggiungi** per scegliere se unire i file di controllo da un disco locale oppure importarli da Archiviazione di Azure. È necessario specificare i dettagli e la chiave dell'account di Archiviazione di Azure.

    3. Dopo aver aggiunto tutti i file da unire, fare clic su **OK** per completare l'operazione di unione.

    4. Il file unito verrà aperto in SSMS, dove potrà essere visualizzato, analizzato ed esportato in un file XEL o CSV o in una tabella.

- Usare Power BI. È possibile visualizzare e analizzare i dati dei log di controllo in Power BI. Per altre informazioni e per accedere a un modello scaricabile, vedere [Analyze audit log data in Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/) (Analizzare i dati di log di controllo in Power BI).
- Scaricare i file di log dal contenitore BLOB di Archiviazione di Azure tramite il portale o usando uno strumento come [Azure Storage Explorer.](https://storageexplorer.com/)
  - Dopo aver scaricato un file di log in locale, fare doppio clic sul file per aprire, visualizzare e analizzare i log in SSMS.
  - È anche possibile scaricare più file contemporaneamente tramite Azure Storage Explorer. Per farlo, fare clic con il pulsante destro del mouse su una sottocartella specifica e scegliere **Salva con nome** per salvarla in una cartella locale.

- Altri metodi:

  - Dopo avere scaricato diversi file o una sottocartella contenente i file di log, è possibile unirli in locale come descritto nelle istruzioni relative all'unione di file di controllo in SSMS riportate in precedenza.
  - Visualizzare i log del controllo BLOB a livello di codice:

    - [Eseguire query sui file di eventi estesi](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) con PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Procedure nell'ambiente di produzione

<!--The description in this section refers to preceding screen captures.-->

#### <a name="auditing-geo-replicated-databases"></a>Controllo dei database con replica geografica

Con i database con replica geografica, quando si abilita il controllo nel database primario il database secondario disporrà di un criterio di controllo identico. È anche possibile impostare il controllo nel database secondario abilitando il controllo nel **server secondario**, in modo indipendente dal database primario.

- A livello di server (**consigliato**): attivare il controllo sia nel **server primario** che nel **server secondario**. I database primari e secondari saranno controllati in modo indipendente in base ai rispettivi criteri a livello di server.
- A livello di database: il controllo a livello di database per i database secondari può essere configurato solo mediante le impostazioni di controllo del database primario.
  - Il controllo deve essere abilitato nello *stesso database primario* e non nel server.
  - Dopo che il controllo è stato abilitato nel database primario, verrà abilitato anche nel database secondario.

    >[!IMPORTANT]
    >In caso di controllo a livello di database, le impostazioni di archiviazione per il database secondario sono identiche a quelle del database primario, e causano traffico tra le aree. È consigliabile abilitare solo il controllo a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.

#### <a name="storage-key-regeneration"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si scrivono i log di controllo nell'archiviazione di Azure, è necessario salvare nuovamente i criteri di controllo quando si aggiornano le chiavi. Il processo è il seguente:

1. Aprire **Dettagli archiviazione**. Nella casella **Chiave di accesso alle risorse di archiviazione** selezionare **Secondaria** e fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.

    ![Riquadro di spostamento](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Passare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso primaria.

    ![Riquadro di spostamento](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Tornare alla pagina di configurazione del controllo, modificare la chiave di accesso alle risorse di archiviazione da secondaria a primaria e quindi fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.
4. Tornare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso secondaria (in preparazione al successivo ciclo di aggiornamento della chiave).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Gestire il controllo di SQL Server e database di AzureManage Azure SQL Server and Database auditing

#### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

**Cmdlet PowerShell (incluso il supporto della clausola WHERE per altri filtri)**:

- [Creare o aggiornare i criteri di controllo del database (Set-AzSqlDatabaseAudit)Create or Update Database Auditing Policy (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Creare o aggiornare i criteri di controllo del server (Set-AzSqlServerAudit)Create or Update Server Auditing Policy (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Ottenere i criteri di controllo del database (Get-AzSqlDatabaseAudit)Get Database Auditing Policy (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Ottenere i criteri di controllo del server (Get-AzSqlServerAudit)Get Server Auditing Policy (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Rimuovere i criteri di controllo del database (Remove-AzSqlDatabaseAudit)Remove Database Auditing Policy (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Rimuovere i criteri di controllo del server (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

#### <a name="using-rest-api"></a>Uso dell'API REST

**API REST**:

- [Creare o aggiornare i criteri controllo del database](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Creare o aggiornare i criteri controllo del server](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i criteri controllo del database](/rest/api/sql/database%20auditing%20settings/get)
- [Ottenere i criteri controllo del server](/rest/api/sql/server%20auditing%20settings/get)

Criteri estesi con il supporto della clausola WHERE per altri filtri:

- [Creare o aggiornare i criteri di controllo estesi del databaseCreate or Update Database *Extended* Auditing Policy](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Creare o aggiornare i criteri di controllo *estesi del* serverCreate or Update Server Extended Auditing Policy](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i criteri di controllo *estesi del* databaseGet Database Extended Auditing Policy](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Ottenere i criteri di controllo estesi del serverGet Server *Extended* Auditing Policy](/rest/api/sql/server%20auditing%20settings/get)

#### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

È possibile gestire il controllo del database SQL di Azure usando i modelli di [Azure Resource Manager](../azure-resource-manager/management/overview.md), come illustrato negli esempi seguenti:

- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in un account di Archiviazione BLOB di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Gli esempi collegati si trovano in un archivio pubblico esterno e vengono forniti "così com'è", senza garanzia e non sono supportati in alcun programma/servizio di supporto Microsoft.
