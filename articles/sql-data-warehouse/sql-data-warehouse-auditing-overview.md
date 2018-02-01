---
title: Servizio di controllo di Azure SQL Data Warehouse | Microsoft Docs
description: Introduzione al servizio di controllo di Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 01/16/2018
ms.author: rortloff;barbkess
ms.openlocfilehash: 5400f29d8c7579809ef7b2a084115473df7baa85
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Servizio di controllo di Azure SQL Data Warehouse

La funzionalità di controllo di SQL Data Warehouse consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. La funzionalità di controllo di SQL Data Warehouse si integra inoltre con Microsoft Power BI per l'esecuzione di analisi e report.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro protezione Azure</a>.

## <a id="subheading-1"></a>Nozioni di base sul controllo
Il controllo del database SQL Data Warehouse consente di:

* **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
* **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
* **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

È possibile configurare il controllo per le seguenti categorie di eventi:

**SQL normale** e **SQL con parametri** per cui i log di controllo raccolti sono classificati come  

* **Accesso ai dati**
* **Modifiche dello schema (DDL)**
* **Modifiche dei dati (DML)**
* **Account, ruoli e autorizzazioni (DCL)**
* **Stored procedure**, **accesso** e **gestione delle transazioni**.

Per ogni categoria di eventi, il controllo delle operazioni **riuscite** e **non riuscite** viene configurato separatamente.

Per altre informazioni sulle attività e sugli eventi controllati, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">documento di riferimento sul formato dei log di controllo (download di file DOC)</a>.

I log di controllo vengono archiviati nell'account di archiviazione di Azure. È possibile definire un periodo di conservazione del log di controllo.

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti. I criteri di controllo del server predefiniti si applicano a tutti i database di un server per i quali non sono definiti specifici criteri di controllo del database prioritari.

Prima di impostare il controllo, verificare che si stia usando un [client di livello inferiore](sql-data-warehouse-auditing-downlevel-clients.md).

## <a id="subheading-2"></a>Configurare il controllo per il database
1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.
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
I log di controllo vengono aggregati in una raccolta di tabelle di archiviazione con il prefisso **SQLDBAuditLogs** nell'account di archiviazione di Azure scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento come <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Esplora archivi di Azure</a>.

È possibile scaricare un modello di report dashboard preconfigurato in formato <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">foglio di calcolo di Excel</a>, che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download <a href="http://www.microsoft.com/download/details.aspx?id=39379">qui</a>.

Il modello contiene dati di esempio fittizi ed è possibile configurare Power Query per l'importazione diretta del log di controllo dall'account di archiviazione di Azure.

## <a id="subheading-4"></a>Rigenerazione delle chiavi di archiviazione
Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare i criteri. Il processo è il seguente:

1. Nel pannello di configurazione del controllo, descritto nella sezione precedente sulla configurazione del controllo, modificare l'impostazione di **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**.

   ![][4]
2. Passare al pannello di configurazione dell'archiviazione e **rigenerare** la *chiave di accesso primaria*.
3. Tornare al pannello configurazione del controllo. 
4. Modificare l'impostazione di **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **SALVA**.
4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

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


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


