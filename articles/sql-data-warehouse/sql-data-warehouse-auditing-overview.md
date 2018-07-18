---
title: Servizio di controllo di Azure SQL Data Warehouse | Documentazione Microsoft
description: Informazioni sul servizio di controllo e su come impostare il controllo in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 306032ece4feda0e8132db1e95c4a229472e6c04
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2018
ms.locfileid: "34643499"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Servizio di controllo di Azure SQL Data Warehouse

Informazioni sul servizio di controllo e su come impostare il controllo in Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Che cos'è la funzionalità di controllo?
La funzionalità di controllo di SQL Data Warehouse consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Nozioni di base sul controllo
Il controllo del database SQL Data Warehouse consente di:

* **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
* **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
* **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

I log di controllo vengono archiviati nell'account di archiviazione di Azure. È possibile definire un periodo di conservazione del log di controllo.


## <a id="subheading-4"></a>Definire criteri di controllo a livello di server o a livello di database

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti:

* I criteri server **si applicano a tutti i database nuovi ed esistenti** in un server.

* Se *il controllo BLOB del server è abilitato*, *si applica sempre al database*. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

* L'abilitazione del controllo nel database in aggiunta all'abilitazione nel server *non* sostituisce o modifica le impostazioni del controllo BLOB del server. I due controlli coesisteranno. In altre parole, il database viene controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

> [!NOTE]
> È consigliabile abilitare **solo il controllo BLOB a livello di server** e lasciare disabilitato il controllo a livello di database per tutti i database.
> È consigliabile evitare di abilitare contemporaneamente il controllo del server e il controllo del database ad eccezione dei casi seguenti:
> * Si vuole usare un *account di archiviazione* o un *periodo di conservazione* diverso per un database specifico.
> * Per un database specifico si vogliono controllare tipi o categorie di eventi diversi dagli altri database nel server. Ad esempio, potrebbe essere necessario controllare gli inserimenti di tabella solo per un database specifico.
> * Si vuole usare il rilevamento delle minacce, che attualmente è supportato solo con il controllo a livello di database.
>


## <a id="subheading-5"></a>Configurare il controllo a livello di server per tutti i database

Un criterio di controllo del server si applica a **tutti i database esistenti e ai nuovi database creati** nel server.

Nella sezione seguente è descritta la configurazione del controllo mediante il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Accedere all'istanza di **SQL Server** che si vuole controllare (è importante assicurarsi di visualizzare l'istanza di SQL Server, non un database/Data Warehouse specifico). Nel menu **Sicurezza** selezionare **Controllo e rilevamento minacce**.

    ![Riquadro di spostamento][6]
4. Nel pannello *Controllo e rilevamento minacce* selezionare **ON** per **Controllo**. I criteri di controllo verranno applicati a tutti i database esistenti e ai nuovi database creati in questo server.

    ![Riquadro di spostamento][7]
5. Per aprire il pannello **Archiviazione dei log di controllo** selezionare **Dettagli archiviazione**. Selezionare o creare l'account di archiviazione di Azure in cui verranno salvati i log e quindi selezionare il periodo di conservazione al termine del quale verranno eliminati i log meno recenti. Fare quindi clic su **OK**.

    ![Riquadro di spostamento][8]

    > [!IMPORTANT]
    > I log di controllo a livello di server vengono scritti nei **BLOB di accodamento**  nell'archivio BLOB di Azure della sottoscrizione di Azure.
    >
    > * **Archiviazione Premium** attualmente **non è supportata** da Accodare BLOB.
    > * **Storage in VNet** attualmente **non è supportato**.

8. Fare clic su **Save**.



## <a id="subheading-2"></a>Configurare il controllo a livello di database per un singolo database

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti.

È consigliabile usare il controllo a livello di server e non il controllo a livello di database, come descritto in [Definire criteri di controllo a livello di server o a livello di database](#subheading-4)

Prima di impostare il controllo verificare che si stia utilizzando un ["Client di livello inferiore"](sql-data-warehouse-auditing-downlevel-clients.md).


1. Avviare il [portale di Azure](https://portal.azure.com).
2. Passare a **Impostazioni** per l'istanza di SQL Data Warehouse che si vuole controllare. Selezionare **Controllo e rilevamento minacce**.

    ![][1]
3. Successivamente, abilitare il controllo facendo clic sul pulsante **ON** .

    ![][3]
4. Nel pannello di configurazione del controllo selezionare **DETTAGLI ARCHIVIAZIONE** per aprire il pannello Archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure per i log e il periodo di conservazione.
    >[!TIP]
    >Per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

    ![][4]

5. Fare clic sul pulsante **OK** per salvare la configurazione dei dettagli di archiviazione .
6. In **REGISTRAZIONE PER EVENTO** fare clic su **OPERAZIONE RIUSCITA** e **OPERAZIONE NON RIUSCITA** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.
7. Se si sta configurando il controllo per un database, è necessario modificare la stringa di connessione del client per garantire che il controllo dei dati venga acquisito correttamente. Controllare l’argomento [Modificare il nome di dominio di Server completo nella stringa di connessione](sql-data-warehouse-auditing-downlevel-clients.md) per le connessioni di client di livello inferiore.
8. Fare clic su **OK**.

## <a id="subheading-3"></a>Analizzare i log di controllo e i report

###<a name="server-level-policy-audit-logs"></a>Log di controllo dei criteri a livello di server
I log di controllo a livello di server vengono scritti nei **BLOB di accodamento**  nell'archivio BLOB di Azure della sottoscrizione di Azure. Questi log vengono salvati come raccolta di file BLOB in un contenitore denominato **sqldbauditlogs**.

Per altri dettagli sulla gerarchia della cartella di archiviazione, le convenzioni di denominazione e il formato dei log, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

Per visualizzare i log del controllo BLOB sono disponibili diversi metodi:

* Usare **Unisci file di controllo** in SQL Server Management Studio (a partire da SSMS 17):
    1. Dalla barra dei menu di SSMS selezionare **File** > **Apri** > **Unisci file di controllo**.

    2. Verrà visualizzata la finestra di dialogo **Aggiunti file di controllo**. Selezionare una delle opzioni **Aggiungi** per scegliere se unire i file di controllo da un disco locale oppure importarli da Archiviazione di Azure. È necessario specificare i dettagli e la chiave dell'account di Archiviazione di Azure.

    3. Dopo aver aggiunto tutti i file da unire, fare clic su **OK** per completare l'operazione di unione.

    4. Il file unito verrà aperto in SSMS, dove potrà essere visualizzato, analizzato ed esportato in un file XEL o CSV o in una tabella.

* Usare l'[applicazione di sincronizzazione](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) che è stata creata. L'applicazione, eseguita in Azure, utilizza le API pubbliche di Log Analytics per effettuare il push dei log di controllo SQL in Log Analytics, per l'utilizzo tramite il dashboard di Log Analytics.

* Usare Power BI. È possibile visualizzare e analizzare i dati dei log di controllo in Power BI. Per altre informazioni, vedere il post di blog su [Power BI e l'accesso a un modello scaricabile](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Scaricare i file di log dal contenitore BLOB del servizio di archiviazione di Azure tramite il portale o con uno strumento come [Azure Storage Explorer](http://storageexplorer.com/).
    * Dopo aver scaricato un file di log in locale, è possibile fare doppio clic sul file per aprire, visualizzare e analizzare i log in SSMS.
    * È anche possibile scaricare più file contemporaneamente tramite Azure Storage Explorer. Fare clic con il pulsante destro del mouse su una sottocartella specifica e scegliere **Salva con nome** per salvarla in una cartella locale.

* Altri metodi:
   * Dopo avere scaricato diversi file o una sottocartella contenente i file di log, è possibile unirli in locale come descritto nelle istruzioni relative all'unione di file di controllo in SSMS riportate in precedenza.

   * Visualizzare i log del controllo BLOB a livello di codice:

     * Usare la libreria C# del [lettore di eventi estesi](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Eseguire query sui file di eventi estesi](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) con PowerShell.



<br>
###<a name="database-level-policy-audit-logs"></a>Log di controllo dei criteri a livello di database
I log di controllo a livello di database vengono aggregati in una raccolta di tabelle di archiviazione con il prefisso **SQLDBAuditLogs** nell'account di archiviazione di Azure scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento come [Esplora archivi di Azure](http://azurestorageexplorer.codeplex.com).

È possibile scaricare un modello di report dashboard preconfigurato in formato [foglio di calcolo di Excel](http://go.microsoft.com/fwlink/?LinkId=403540), che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download [qui](http://www.microsoft.com/download/details.aspx?id=39379).

Il modello contiene dati di esempio fittizi ed è possibile configurare Power Query per l'importazione diretta del log di controllo dall'account di archiviazione di Azure.

## <a id="subheading-4"></a>Rigenerazione delle chiavi di archiviazione
Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare i criteri. Il processo è il seguente:

1. Nel pannello di configurazione del controllo, descritto nella sezione precedente sulla configurazione del controllo, modificare l'impostazione di **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**.

   ![][4]
2. Passare al pannello di configurazione dell'archiviazione e **rigenerare** la *chiave di accesso primaria*.
3. Tornare al pannello configurazione del controllo.
4. Modificare l'impostazione di **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **SALVA**.
4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *Chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

## <a id="subheading-5"></a>Automazione (API REST/PowerShell)
È possibile configurare il controllo in Azure SQL Data Warehouse anche con gli strumenti di automazione seguenti:

* **Cmdlet di PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Supporto client di livello inferiore per controllo e maschera dati dinamica
Il servizio di controllo funziona con i client SQL che supportano il reindirizzamento TDS.

I client che implementano TDS 7.4 supportano in genere anche il reindirizzamento. Rappresentano un'eccezione JDBC 4.0, in cui non è del tutto supportata la funzionalità di reindirizzamento, e Tedious per Node.JS, in cui non è implementato il reindirizzamento.

Per i "client di livello inferiore" che supportano TDS versione 7.3 e precedenti, modificare il nome di dominio completo del server nella stringa di connessione, come indicato di seguito:

- Nome di dominio completo del server originale nella stringa di connessione: <*server name*>.database.windows.net
- Nome di dominio completo del server modificato nella stringa di connessione: <*server name*>.database.**secure**.windows.net

Un elenco parziale di "client di livello inferiore" include:

* .NET 4.0 e versioni precedenti,
* ODBC 10.0 e versioni precedenti.
* JDBC (mentre JDBC supporta TDS 7.4, la funzionalità di reindirizzamento TDS non è del tutto supportata)
* Tedious (per Node.JS)

**Nota:** la precedente modifica del nome di dominio completo del server può risultare utile per applicare criteri di controllo a livello di SQL Server senza dover eseguire una procedura di configurazione in ogni database (attenuazione temporanea).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
