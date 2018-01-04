---
title: Servizio di controllo di Azure SQL Data Warehouse | Documentazione Microsoft
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
ms.date: 08/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: f851c82ebeaa647f663d499a4d327c3479e36121
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Servizio di controllo di Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Controllo](sql-data-warehouse-auditing-overview.md)
> * [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)
> 
> 

La funzionalità di controllo di SQL Data Warehouse consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. La funzionalità di controllo di SQL Data Warehouse si integra inoltre con Microsoft Power BI per l'esecuzione di analisi e report drill-down.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro protezione Azure</a>.

* [Nozioni di base sul controllo del database]
* [Configurare il controllo per il database]
* [Analizzare i log di controllo e i report]

## <a id="subheading-1"></a>Nozioni di base sul controllo del database di SQL Data Warehouse
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

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. I criteri di controllo del server predefiniti si applicano a tutti i database di un server per i quali non sono definiti specifici criteri di controllo del database che hanno la precedenza.

Prima di impostare il controllo, verificare che si stia usando un [client di livello inferiore](sql-data-warehouse-auditing-downlevel-clients.md).

## <a id="subheading-2"></a>Configurare il controllo per il database
1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.
2. Passare al pannello **Impostazioni** dell'istanza di SQL Data Warehouse che si vuole controllare. Nel pannello **Impostazioni** selezionare **Controllo e rilevamento minacce**.
   
    ![][1]
3. Successivamente, abilitare il controllo facendo clic sul pulsante **ON** .
   
    ![][3]
4. Nel pannello di configurazione del controllo selezionare **DETTAGLI ARCHIVIAZIONE** per aprire il pannello Archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione. 
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

1. Nel pannello di configurazione del controllo (descritto in precedenza nella sezione sulla configurazione del controllo) cambiare **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**.

   ![][4]
2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.
3. Tornare al pannello di configurazione del controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **SALVA**.
4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *Chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

## <a id="subheading-5"></a>Automazione (API REST/PowerShell)
È possibile configurare il controllo in Azure SQL Data Warehouse anche con gli strumenti di automazione seguenti:

* **Cmdlet di PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

<!--Anchors-->
[Nozioni di base sul controllo del database]: #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy