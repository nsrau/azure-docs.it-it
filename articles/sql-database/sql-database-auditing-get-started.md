---
title: Introduzione al controllo del database SQL | Documentazione Microsoft
description: Introduzione al controllo del database SQL
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 7b3c6d29ad7ded2ea65f378a1f807235c688d738
ms.openlocfilehash: 9f51fcdb291021064dc02a71afdf849f1f4a3d85


---
# <a name="get-started-with-sql-database--auditing"></a>Introduzione al controllo del database SQL
Il controllo del database SQL di Azure tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure dell'utente.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Il controllo abilita e facilita il rispetto degli standard di conformità, ma non garantisce la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Panoramica del controllo del database SQL di Azure]
* [Configurare il controllo per il database]
* [Analizzare i log di controllo e i report]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Panoramica del controllo del database SQL di Azure
Il controllo del database SQL consente di:

* **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
* **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
* **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

Esistono due **metodi di controllo**:

* **Controllo BLOB**: i log vengono scritti nell'Archiviazione BLOB di Azure. Questo è un metodo di controllo più recente che fornisce **prestazioni più elevate**, supporta **una maggiore granularità del controllo a livello di oggetto** ed è **più conveniente**.
* **Controllo Tabella**: i log vengono scritti nell'archiviazione tabelle di Azure.

È possibile configurare il controllo per diversi tipi di categorie di eventi, come spiegato nella sezione [Configurare il controllo per il database](#subheading-2).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo del server predefinito si applica a tutti i database esistenti e ai nuovi database creati in un server.

## <a name="a-idsubheading-2aset-up-auditing-for-your-database"></a><a id="subheading-2"></a>Configurare il controllo per il database
Nella sezione seguente è descritta la configurazione del controllo utilizzando il portale di Azure.

### <a name="a-idsubheading-2-1blob-auditinga"></a><a id="subheading-2-1">Controllo BLOB</a>
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione del database SQL / SQL Server che si vuole controllare. Nel pannello Impostazioni selezionare **Controllo e rilevamento delle minacce**.
   
    <a id="auditing-screenshot"></a>
    ![Riquadro di spostamento][1]
3. Nel pannello per la configurazione del controllo del database è possibile selezionare la casella di controllo **Eredita impostazioni di controllo del server** per indicare che il database sarà controllato in base alle impostazioni del relativo server. Se questa opzione è selezionata, viene visualizzato un collegamento per **visualizzare le impostazioni di controllo del server** che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.
   
    ![Riquadro di spostamento][2]
4. Se si vuole abilitare il controllo BLOB a livello di database, in aggiunta o al posto del controllo a livello di server, **deselezionare** l'opzione **Eredita impostazioni di controllo del server**, **attivare** Controllo e scegliere il tipo di controllo **BLOB**.
   
   > Se il controllo BLOB del server è abilitato, il controllo configurato del database coesisterà con il controllo BLOB del server.  
   > 
   > 
   
    ![Riquadro di spostamento][3]
5. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione, dopo il quale verranno eliminati i vecchi log, quindi fare clic su **OK** nella parte inferiore. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.
   
    <a id="storage-screenshot"></a>
    ![Riquadro di spostamento][4]
6. Se si vuole personalizzare gli eventi controllati, è possibile farlo tramite l'API di PowerShell o REST: per altre informazioni, vedere la sezione [Automazione (API di PowerShell/REST)](#subheading-7).
7. Fare clic su **Save**.

### <a name="a-idsubheading-2-2table-auditinga"></a><a id="subheading-2-2">Controllo Tabella</a>
> [!NOTE]
> Prima di impostare il **controllo Tabella** verificare se è in uso un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Inoltre, se le impostazioni del firewall sono restrittive, tenere presente che l'[endpoint IP del database verrà modificato](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) quando si abilita il controllo Tabella.
> 
> 

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione del database SQL / SQL Server che si vuole controllare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce** (*[vedere la schermata illustrata nella sezione Controllo BLOB](#auditing-screenshot)*).
3. Nel pannello per la configurazione del controllo del database è possibile selezionare la casella di controllo **Eredita impostazioni di controllo del server** per indicare che il database sarà controllato in base alle impostazioni del relativo server. Se questa opzione è selezionata, viene visualizzato un collegamento per **visualizzare le impostazioni di controllo del server** che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.
   
    ![Riquadro di spostamento][2]
4. Se si preferisce non ereditare le impostazioni di controllo dal server, **deselezionare** l'opzione **Eredita impostazioni di controllo dal server**, **attivare** Controllo e scegliere il tipo di controllo **Tabella**.
   
    ![Riquadro di spostamento][3-tbl]
5. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione, dopo il quale verranno eliminati i vecchi log. **Suggerimento:** per sfruttare al meglio i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati (*[vedere la schermata illustrata nella sezione Controllo BLOB](#storage-screenshot)*).
6. Fare clic su **Eventi controllati** per personalizzare gli eventi da controllare. Nel pannello **Registrazione per evento** fare clic su **Operazione riuscita** e **Operazione non riuscita** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.
   
   > È possibile personalizzare gli eventi controllati anche tramite l'API di PowerShell o REST: per altre informazioni vedere la sezione [Automazione (API di PowerShell/REST)](#subheading-7).
   > 
   > 
   
    ![Riquadro di spostamento][5]
7. Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità **Rilevamento delle minacce** (anteprima) e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzione di Rilevamento delle minacce consente di ricevere avvisi proattivi sulle attività del database anomale che possono indicare potenziali minacce alla protezione. Vedere l' [introduzione a Threat Detection](sql-database-threat-detection-get-started.md) per altri dettagli.
8. Fare clic su **Save**.

## <a name="a-idsubheading-3aanalyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizzare i log di controllo e i report
I log di controllo vengono aggregati nell'account di archiviazione di Azure selezionato durante la configurazione.

È possibile esplorare i log di controllo con uno strumento come [Esplora archivi di Azure](http://storageexplorer.com/).

Vedere di seguito le specifiche per l'analisi dei log di controllo **Tabella** e **BLOB**.

### <a name="a-idsubheading-3-1blob-auditinga"></a><a id="subheading-3-1">Controllo BLOB</a>
I log di controllo BLOB vengono salvati come un insieme di file BLOB in un contenitore denominato "**sqldbauditlogs**".

Per altre informazioni sulla gerarchia delle cartelle di archiviazione dei log di controllo BLOB, le convenzioni di denominazione BLOB e il formato dei log, vedere le [informazioni di riferimento sul formato BLOB dei log di controllo (download del file doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Esistono diversi metodi per visualizzare i log di controllo BLOB:

1. Tramite il portale di Azure: **vedere il metodo (1) nella [sezione Controllo Tabella](#activity-ui) sotto** (il download dei file Excel non è supportato).
2. Scaricare i file di log dal contenitore BLOB di Archiviazione di Azure tramite il portale o tramite uno strumento come [Esplora archivi di Azure](http://storageexplorer.com/).
   
    Dopo aver scaricato il file di log in locale, è possibile fare doppio clic su file per aprire, visualizzare e analizzare i log in SSMS.
   
    Altri metodi:
   
   * È possibile **scaricare più file contemporaneamente** tramite Esplora archivi di Azure, fare clic su una sottocartella specifica (ad esempio una sottocartella che include tutti i file di log per una determinata data) e scegliere "Salva con nome" per salvare i dati in una cartella locale.
     
       Dopo aver scaricato diversi file (o un'intera giornata, come descritto in precedenza), è possibile unirli in locale nel modo seguente:
     
       **Aprire SSMS -> File -> Apri -> Unisci eventi estesi -> Scegliere tutti i file da unire**
   * A livello di codice:
     
     * Lettore di eventi estesi **libreria C#** ([altre informazioni qui](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * Esecuzione di query sui file di eventi estesi mediante **PowerShell** ([altre informazioni qui](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

### <a name="a-idsubheading-3-2table-auditinga"></a><a id="subheading-3-2">Controllo Tabella</a>
I log di controllo Tabella vengono salvati come una raccolta di tabelle di Azure con prefisso **SQLDBAuditLogs**.

Per altre informazioni sui log di controllo in formato Tabella, vedere le [informazioni di riferimento sul formato Tabella dei log di controllo (download del file doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Esistono diversi metodi per visualizzare i log di controllo Tabella:

<a id="activity-ui"></a>

1. Tramite il [portale di Azure](https://portal.azure.com): nella parte superiore del pannello **Controllo e rilevamento minacce** fare clic su **Altro** e quindi su **Esplora**. Verrà aperto il pannello **Record di controllo** in cui sarà possibile visualizzare i log.
   
   * Si può scegliere di visualizzare date specifiche facendo clic su **Filtro** nell'area superiore del pannello Record di controllo
   * È possibile scaricare e visualizzare i log di controllo in formato Excel facendo clic su **Apri in Excel** nell'area superiore del pannello Record di controllo
     
     ![Riquadro di spostamento][7]
2. In alternativa si può scaricare un modello di report preconfigurato in formato [foglio di calcolo di Excel scaricabile](http://go.microsoft.com/fwlink/?LinkId=403540) che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download [qui](http://www.microsoft.com/download/details.aspx?id=39379).
   
    È possibile anche importare i registri di controllo nel modello di Excel direttamente dall'account di Archiviazione di Azure usando Power Query. È quindi possibile esplorare i record di controllo e creare dashboard e report sui dati di log.
   
    ![Riquadro di spostamento][9]

## <a name="a-idsubheading-5apractices-for-usage-in-production"></a><a id="subheading-5"></a>Procedure per l'uso in produzione
<!--The description in this section refers to screen captures above.-->

### <a name="a-idsubheading-6auditing-geo-replicated-databasesa"></a><a id="subheading-6">Controllo dei database con replica geografica</a>
Se si usano database con replica geografica è possibile configurare il controllo nel database primario, nel database secondario o in entrambi, a seconda del tipo di controllo.

**Controllo Tabella**: è possibile configurare un criterio separato, a livello di database o a livello di server, per ciascuno dei due database (primario e secondario) come descritto nella sezione [Configurare il controllo per il database](#subheading-2-2).

**Controllo BLOB**: seguire queste istruzioni:

1. **Database primario**: attivare il **controllo BLOB** sul server o sul database stesso, come descritto nella sezione [Configurare il controllo per il database](#subheading-2-1).
2. **Database secondario**: il controllo BLOB può essere attivato/disattivato solo dalle impostazioni di controllo del database primario.
   
   * Attivare il **controllo BLOB** sul **database primario**, come descritto nella sezione [Configurare il controllo per il database](#subheading-2-1). **Nota:** il controllo BLOB deve essere abilitato nel database stesso e non nel server.
   * Dopo che il controllo BLOB è stato abilitato nel database primario, diventare abilitato anche nel database secondario.
   * **Importante:** per impostazione predefinita, le **impostazioni di archiviazione** per il database secondario saranno identiche a quelle del database primario generando **traffico tra aree**. È possibile evitare questo problema attivando il controllo BLOB sul **server secondario** e configurando una **risorsa di archiviazione locale** nelle impostazioni di archiviazione del server secondario (in questo modo sarà ignorato il percorso di archiviazione per il database secondario e di conseguenza ogni database salverà i log di controllo in una risorsa di archiviazione locale).  

<br>

### <a name="a-idsubheading-6storage-key-regenerationa"></a><a id="subheading-6">Rigenerazione delle chiavi di archiviazione</a>
Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio di controllo. Il processo è il seguente:

1. Nel pannello delle impostazioni di archiviazione cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **OK** in fondo. Fare clic su **SALVA** nella parte superiore del pannello di configurazione del controllo.
   
    ![Riquadro di spostamento][6]
2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.
   
    ![Riquadro di spostamento][8]
3. Tornare al pannello di configurazione del controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **OK** in fondo. Fare clic su **SALVA** nella parte superiore del pannello di configurazione del controllo.
4. Tornare al pannello di configurazione e **rigenerare** il *Tasto di scelta secondario* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

## <a name="a-idsubheading-7aautomation-powershell--rest-api"></a><a id="subheading-7"></a>Automazione (API di PowerShell/REST)
È possibile configurare il controllo nel database SQL di Azure anche mediante gli strumenti di automazione seguenti:

1. **Cmdlet di PowerShell**
   
   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **API REST: controllo BLOB**
   
   * [Creare o aggiornare i criteri controllo BLOB del database](https://msdn.microsoft.com/en-us/library/azure/mt695939.aspx)
   * [Creare o aggiornare i criteri controllo BLOB del server](https://msdn.microsoft.com/en-us/library/azure/mt771861.aspx)
   * [Ottenere i criteri controllo BLOB del database](https://msdn.microsoft.com/en-us/library/azure/mt695938.aspx)
   * [Ottenere i criteri controllo BLOB del server](https://msdn.microsoft.com/en-us/library/azure/mt771860.aspx)
   * [Ottenere il risultato dell'operazione di controllo BLOB del server](https://msdn.microsoft.com/en-us/library/azure/mt771862.aspx)
3. **API REST: controllo Tabella**
   
   * [Creare o aggiornare i criteri controllo del database](https://msdn.microsoft.com/en-us/library/azure/mt604471.aspx)
   * [Creare o aggiornare i criteri controllo del server](https://msdn.microsoft.com/en-us/library/azure/mt604383.aspx)
   * [Ottenere i criteri controllo del database](https://msdn.microsoft.com/en-us/library/azure/mt604381.aspx)
   * [Ottenere i criteri controllo del server](https://msdn.microsoft.com/en-us/library/azure/mt604382.aspx)

<!--Anchors-->
[Panoramica del controllo del database SQL di Azure]: #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx



<!--HONumber=Dec16_HO1-->


