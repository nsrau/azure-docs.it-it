<properties
   pageTitle="Servizio di controllo di Azure SQL Data Warehouse | Microsoft Azure"
   description="Introduzione al servizio di controllo di Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/31/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# Servizio di controllo di Azure SQL Data Warehouse
Il servizio di controllo di Azure SQL Data Warehouse tiene traccia degli eventi di database e scrive gli eventi controllati in un log di controllo nell'account di Archiviazione di Azure.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro protezione Azure</a>.

+ [Nozioni di base sul controllo del database]
+ [Configurare il controllo per il database]
+ [Analizzare i log di controllo e i report]

##<a id="subheading-1"></a>Nozioni di base sul controllo del database di SQL Data Warehouse


Il controllo del database SQL Data Warehouse consente di:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

È possibile configurare il controllo per le seguenti categorie di eventi:

**SQL normale** e **SQL con parametri** per cui i log di controllo raccolti sono classificati come

- **Accesso ai dati**
- **Modifiche dello schema (DDL)**
- **Modifiche dei dati (DML)**
- **Account, ruoli e autorizzazioni (DCL)**
- **Stored procedure**, **accesso** e **gestione delle transazioni**.

Per ogni categoria di eventi, il controllo delle operazioni **riuscite** e **non riuscite** viene configurato separatamente.

Per altre informazioni sulle attività e sugli eventi controllati, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">riferimento sul formato dei log di controllo (download di file doc)</a>.

I log di controllo vengono archiviati nell'account di archiviazione di Azure. È possibile definire un periodo di conservazione del log di controllo.

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo del server predefinito verrà applicato a tutti i database di un server che non dispone di un criterio di controllo di override del database.

Prima di impostare il controllo verificare che si stia utilizzando un ["Client di livello inferiore"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurare il controllo per il database

1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

2. Passare al pannello di configurazione del database SQL Data Warehouse / SQL Server che si vuole controllare. Fare clic sul pulsante **Impostazioni** nella parte superiore e quindi nel pannello Impostazioni e selezionare **Controllo**.

	![][1]

3. Nel pannello di configurazione del controllo, deselezionare innanzitutto la casella di controllo **Ereditare le impostazioni di controllo da Server**. In questo modo è possibile specificare le impostazioni per un determinato database.

	![][2]

4. Successivamente, abilitare il controllo facendo clic sul pulsante **ON** .

	![][3]

5. Nel pannello di configurazione del controllo, selezionare i **DETTAGLI DI ARCHIVIAZIONE** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

	![][4]

6. Fare clic sul pulsante **OK** per salvare la configurazione dei dettagli di archiviazione .


7. In **REGISTRAZIONE PER EVENTO**, fare clic su **OPERAZIONE RIUSCITA** e **OPERAZIONE NON RIUSCITA** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.


8. Se si sta configurando il controllo per un database, è necessario modificare la stringa di connessione del client per garantire che il controllo dei dati venga acquisito correttamente. Controllare l’argomento [Modificare il nome di dominio di Server completo nella stringa di connessione](sql-data-warehouse-auditing-downlevel-clients.md) per le connessioni di client di livello inferiore.

9. Fare clic su **OK**.


##<a id="subheading-3">Analizzare i log di controllo e i report</a>

I log di controllo vengono aggregati in una raccolta di tabelle di archiviazione con il prefisso **SQLDBAuditLogs** nell'account di archiviazione di Azure scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento come <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Esplora archivi di Azure</a>.

È possibile scaricare un modello di report dashboard preconfigurato in formato <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">foglio di calcolo di Excel</a>, che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download <a href="http://www.microsoft.com/download/details.aspx?id=39379">qui</a>.

Il modello contiene dati di esempio fittizi ed è possibile configurare Power Query per l'importazione diretta del log di controllo dall'account di archiviazione di Azure.

Per istruzioni dettagliate sull'uso del modello di report, scaricare il documento con la <a href="http://go.microsoft.com/fwlink/?LinkId=506731">procedura</a>.

![][5]


##<a id="subheading-4">Procedure per l'uso in produzione</a>
La descrizione in questa sezione fa riferimento alle schermate precedenti. È possibile usare il <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a>.


##<a id="subheading-5"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che si aggiornino periodicamente le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio. Il processo è il seguente:


1. Nel pannello di configurazione di controllo (descritto sopra nella sezione sulla configurazione del controllo) cambiare **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**. ![][4]
2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.

3. Tornare al pannello di configurazione di controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e premere **SALVA**.

4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *Chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

##<a id="subheading-6"></a>Automazione
Sono disponibili numerosi cmdlet di PowerShell che è possibile usare per configurare il controllo nel database SQL di Azure. Per accedere ai cmdlet di controllo, è necessario che PowerShell sia in esecuzione in modalità Gestione risorse di Azure.

> [AZURE.NOTE] Il modulo [Gestione risorse di Azure](https://msdn.microsoft.com/library/dn654592.aspx) è al momento in anteprima. Potrebbe non fornire le stesse funzionalità di gestione del modulo di Azure.

Dopo aver avviato la modalità Gestione risorse di Azure, eseguire `Get-Command *AzureSql*` per visualizzare l'elenco dei cmdlet disponibili.


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

<!---HONumber=AcomDC_0601_2016-->