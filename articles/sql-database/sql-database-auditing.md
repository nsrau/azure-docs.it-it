---
title: Introduzione al controllo del database SQL di Azure | Microsoft Docs
description: Introduzione al servizio di controllo del database SQL di Azure
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: f4324a59b5fa4c2e1ab5b1d7fc7e5fe986ea80f8
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="get-started-with-sql-database-auditing"></a>Introduzione al controllo del database SQL
Il servizio di controllo del database SQL di Azure tiene traccia degli eventi di database e li registra in un log di controllo nell'account di archiviazione di Azure dell'utente. Inoltre, il servizio di controllo:

* Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che potrebbero indicare problemi aziendali o possibili violazioni della sicurezza.

* Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Panoramica del servizio di controllo del database SQL di Azure
È possibile usare il servizio di controllo del database SQL per eseguire le operazioni seguenti:


* **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
* **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
* **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

È possibile configurare il controllo per diversi tipi di categorie di eventi, come spiegato nella sezione [Configurare il controllo per il database](#subheading-2).

I log di controllo vengono scritti nell'archivio BLOB di Azure della sottoscrizione di Azure.


## <a id="subheading-8"></a>Definire criteri di controllo a livello di server o a livello di database

I criteri di controllo possono essere definiti per un database specifico o come criteri server predefiniti:

* I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

* Se abilitato, il *controllo BLOB del server* *si applica sempre al database*. Il controllo verrà quindi eseguito sul database indipendentemente dalle impostazioni di controllo del database.

* L'abilitazione del controllo BLOB nel database in aggiunta all'abilitazione nel server *non* sostituisce o modifica le impostazioni del controllo BLOB del server. I due controlli coesisteranno. In altre parole, il database verrà controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

   > [!NOTE]
   > È consigliabile evitare di abilitare contemporaneamente il controllo BLOB del server e il controllo BLOB del database ad eccezione dei casi seguenti:
    > * Si vuole usare un *account di archiviazione* o un *periodo di conservazione* diverso per un database specifico.
    > * Per un database specifico si vogliono controllare tipi o categorie di eventi diversi da quelli controllati per gli altri database nel server. Ad esempio, potrebbe essere necessario controllare gli inserimenti di tabella solo per un database specifico.
   > 
   > Negli altri casi, è consigliabile abilitare solo il controllo BLOB a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.


## <a id="subheading-2"></a>Configurare il controllo per il database
Nella sezione seguente è descritta la configurazione del controllo mediante il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al pannello **Impostazioni** del database o del server SQL che si vuole controllare. Nel pannello **Impostazioni** selezionare **Controllo e rilevamento minacce**.

    <a id="auditing-screenshot"></a>
    ![Riquadro di spostamento][1]
3. Se si preferisce configurare criteri di controllo del server, che verranno applicati a tutti i database nuovi ed esistenti nel server, è possibile selezionare il collegamento **Visualizza impostazioni del server** nel pannello relativo al controllo del database. Si possono quindi visualizzare o modificare le impostazioni di controllo del server.

    ![Riquadro di spostamento][2]
4. Se si preferisce abilitare il controllo BLOB a livello di database, in aggiunta o in sostituzione del controllo a livello di server, selezionare **SÌ** per **Controllo** e **BLOB** per **Tipo di controllo**.

    Se il controllo BLOB del server è abilitato, il controllo configurato del database coesisterà con il controllo BLOB del server.  

    ![Riquadro di spostamento][3]
5. Per aprire il pannello **Archiviazione dei log di controllo** selezionare **Dettagli archiviazione**. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e quindi il periodo di conservazione al termine del quale verranno eliminati i log meno recenti. Fare quindi clic su **OK**. 
   >[!TIP] 
   >Per sfruttare al massimo i modelli di report di controllo, usare lo stesso account di archiviazione per tutti i database controllati. 

    <a id="storage-screenshot"></a>
    ![Riquadro di spostamento][4]
6. Per personalizzare gli eventi controllati, è possibile usare PowerShell o l'API REST. Per altre informazioni, vedere la sezione [Automazione (API REST/PowerShell)](#subheading-7).
7. Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità di rilevamento delle minacce e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. Quando si usa il rilevamento delle minacce, si ricevono avvisi proattivi sulle attività di database anomale che possono indicare potenziali minacce per la sicurezza. Per altri dettagli, vedere l'[introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md).
8. Fare clic su **Salva**.





## <a id="subheading-3"></a>Analizzare i log di controllo e i report
I log di controllo vengono aggregati nell'account di archiviazione di Azure selezionato durante la configurazione. È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](http://storageexplorer.com/).

I log del controllo BLOB vengono salvati come raccolta di file BLOB in un contenitore denominato **sqldbauditlogs**.

Per altri dettagli sulla gerarchia della cartella di archiviazione dei log del controllo BLOB, le convenzioni di denominazione dei BLOB e il formato dei log, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB (download di file con estensione docx)](https://go.microsoft.com/fwlink/?linkid=829599).

Per visualizzare i log del controllo BLOB sono disponibili diversi metodi:

* Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore del pannello **Controllo e rilevamento minacce** del database fare clic su **Visualizza log di controllo**.

    ![Riquadro di spostamento][7]

    Verrà aperto il pannello **Record di controllo**, da cui sarà possibile visualizzare i log.

    - È possibile visualizzare date specifiche facendo clic su **Filtro** nella parte superiore del pannello **Record di controllo**.
    - È possibile passare dai record di controllo creati dai criteri del server a quelli creati dai criteri del database e viceversa.

       ![Riquadro di spostamento][8]

* Usare la funzione di sistema **sys.fn_get_audit_file** (T-SQL) per tornare ai dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere la [documentazione su sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Usare **Unisci file di controllo** in SQL Server Management Studio (a partire da SSMS 17):  
    1. Dalla barra dei menu di SSMS selezionare **File** > **Apri** > **Unisci file di controllo**.

        ![Riquadro di spostamento][9]
    2. Verrà visualizzata la finestra di dialogo **Aggiunti file di controllo**. Selezionare una delle opzioni **Aggiungi** per scegliere se unire i file di controllo da un disco locale oppure importarli da Archiviazione di Azure (sarà necessario specificare i dettagli e la chiave dell'account di archiviazione di Azure).

    3. Dopo aver aggiunto tutti i file da unire, fare clic su **OK** per completare l'operazione di unione.

    4. Il file unito verrà aperto in SSMS, dove potrà essere visualizzato, analizzato ed esportato in un file XEL o CSV o in una tabella.

* Usare l'[applicazione di sincronizzazione](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) che è stata creata. L'applicazione viene eseguita in Azure e utilizza le API pubbliche di Operations Management Suite (OMS) Log Analytics per effettuare il push dei log di controllo SQL in OMS, per l'utilizzo tramite il dashboard di OMS Log Analytics. 

* Usare Power BI. È possibile visualizzare e analizzare i dati dei log di controllo in Power BI. Per altre informazioni, vedere il post di blog su [Power BI e l'accesso a un modello scaricabile](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Scaricare i file di log dal contenitore BLOB del servizio di archiviazione di Azure tramite il portale o con uno strumento come [Azure Storage Explorer](http://storageexplorer.com/).
    * Dopo aver scaricato un file di log in locale, è possibile fare doppio clic sul file per aprire, visualizzare e analizzare i log in SSMS.
    * È anche possibile scaricare più file contemporaneamente tramite Azure Storage Explorer. Fare clic con il pulsante destro del mouse su una sottocartella specifica, ad esempio una sottocartella contenente tutti i file di log per una determinata data, e scegliere **Salva con nome** per salvarla in una cartella locale.

* Altri metodi:
   * Dopo aver scaricato diversi file (o una sottocartella contenente i file di log per un intero giorno, come descritto nella voce precedente di questo elenco), è possibile unirli in locale come descritto nelle istruzioni relative all'unione di file di controllo in SSMS riportate sopra.

   * Visualizzare i log del controllo BLOB a livello di codice:

     * Usare la libreria C# del [lettore di eventi estesi](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Eseguire query sui file di eventi estesi](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) con PowerShell.




## <a id="subheading-5"></a>Procedure nell'ambiente di produzione
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Controllo dei database con replica geografica</a>
Se si usano database con replica geografica, è possibile configurare il controllo nel database primario, nel database secondario o in entrambi, a seconda del tipo di controllo.

Seguire le istruzioni di seguito, tenendo presente che il controllo BLOB può essere attivato o disattivato solo dalle impostazioni di controllo del database primario:

* **Database primario**. Attivare il controllo BLOB nel server o nel database stesso, come descritto nella sezione [Configurare il controllo per il database](#subheading-2).
* **Database secondario**. Attivare il controllo BLOB nel database primario, come descritto nella sezione [Configurare il controllo per il database](#subheading-2). 
   * Il controllo BLOB deve essere abilitato nello *stesso database primario* e non nel server.
   * Dopo che il controllo BLOB è stato abilitato nel database primario, verrà abilitato anche nel database secondario.

     >[!IMPORTANT]
     >Per impostazione predefinita, le impostazioni di archiviazione per il database secondario sono identiche a quelle del database primario, e causano traffico tra le aree. È possibile evitare questo problema abilitando il controllo BLOB nel server secondario e configurando una risorsa di archiviazione locale nelle impostazioni di archiviazione del server secondario. In questo modo verrà ignorato il percorso di archiviazione per il database secondario e di conseguenza ogni database salverà i propri log di controllo in una risorsa di archiviazione locale.  
<br>

### <a id="subheading-6">Rigenerazione delle chiavi di archiviazione</a>
Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente i criteri di controllo. Il processo è il seguente:

1. Aprire il pannello **Dettagli archiviazione**. Nella casella **Chiave di accesso alle risorse di archiviazione** selezionare **Secondaria** e fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore del pannello di configurazione del controllo.

    ![Riquadro di spostamento][5]
2. Passare al pannello di configurazione dell'archiviazione e rigenerare la chiave di accesso primaria.

    ![Riquadro di spostamento][6]
3. Tornare al pannello di configurazione del controllo, modificare la chiave di accesso alle risorse di archiviazione da secondaria a primaria e quindi fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore del pannello di configurazione del controllo.
4. Tornare al pannello di configurazione dell'archiviazione e rigenerare la chiave di accesso secondaria, in preparazione al successivo ciclo di aggiornamento della chiave.

## <a id="subheading-7"></a>Automazione (API REST/PowerShell)
È possibile configurare il controllo nel database SQL di Azure anche con gli strumenti di automazione seguenti.

* **Cmdlet di PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

   Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

* **API REST per il controllo BLOB**:

   * [Creare o aggiornare i criteri controllo BLOB del database](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Creare o aggiornare i criteri controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Ottenere i criteri controllo BLOB del database](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Ottenere i criteri controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Ottenere il risultato dell'operazione di controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

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

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy

