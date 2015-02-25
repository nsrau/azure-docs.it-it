<properties 
	pageTitle="Ripristino di un sito Web di Microsoft Azure" 
	description="Informazioni su come ripristinare i siti Web di Azure da un backup." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Ripristino di un sito Web di Microsoft Azure

Questo articolo illustra come ripristinare un sito Web precedentemente sottoposto a backup mediante la funzionalità di backup di Siti Web di Azure. Per altre informazioni, vedere [Backup di Siti Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-backup/). 

La funzionalità di ripristino di Siti Web di Azure consente di ripristinare su richiesta lo stato precedente di un sito Web oppure di creare un nuovo sito Web basato su un backup del sito originale. La creazione di un nuovo sito Web eseguito in parallelo con l'ultima versione può essere utile per l'esecuzione di test A/B.

La funzionalità di ripristino, disponibile nella scheda Backup del portale Siti Web di Azure, è disponibile solo in modalità Standard.

##Contenuto dell'articolo
- [Per ripristinare un sito Web di Azure da un backup eseguito in precedenza](#PreviousBackup)
- [Per ripristinare un sito Web di Azure direttamente da un account di archiviazione](#StorageAccount)
- [Scegliere le impostazioni di ripristino del sito Web e avviare l'operazione di ripristino](#RestoreSettings)
- [Visualizzare i registri operazioni](#OperationLogs)


<a name="PreviousBackup"></a>
##Per ripristinare un sito Web di Azure da un backup eseguito in precedenza

1. Nella scheda **Backup** fare clic su **Ripristina ora** nella barra dei comandi nella parte inferiore della pagina del portale. Viene visualizzata la finestra di dialogo **Ripristina ora**.
	
	![Choose backup source][ChooseBackupSource]
	
2. In **Scegliere l'origine del backup** selezionare **Backup precedente per questo sito Web**.
3. Selezionare la data del backup da ripristinare, quindi fare clic sulla freccia destra per continuare.
4. Attenersi alla procedura riportata nella sezione [Scegliere le impostazioni di ripristino del sito Web](#RestoreSettings) più avanti in questo articolo.

<a name="StorageAccount"></a>
##Per ripristinare un sito Web di Azure direttamente da un account di archiviazione

1. Nella scheda **Backup** fare clic su **Ripristina ora** nella barra dei comandi nella parte inferiore della pagina del portale. Viene visualizzata la finestra di dialogo **Ripristina ora**.
	
	![Choose backup source][ChooseBackupSource]
	
2. In **Scegliere l'origine del backup** selezionare **File dell'account di archiviazione**. In questa area è possibile specificare direttamente l'URL del file dell'account di archiviazione oppure fare clic sull'icona della cartella per passare all'archiviazione BLOB e specificare il file di backup. In questo esempio viene selezionata l'icona della cartella.
	
	![Storage Account File][StorageAccountFile]
	
3. Fare clic sull'icona della cartella per aprire la finestra di dialogo **Sfoglia archiviazione cloud**.
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. Espandere il nome dell'account di archiviazione da usare e quindi selezionare **websitebackups**, che contiene i backup dell'utente.
5. Selezionare il file ZIP contenente il backup da ripristinare e quindi fare clic su **Apri**.
6. Il file dell'account di archiviazione verrà selezionato e visualizzato nella casella. Fare clic sulla freccia destra per continuare.
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. Continuare con la seguente sezione [Scegliere le impostazioni di ripristino del sito Web e avviare l'operazione di ripristino](#RestoreSettings).

<a name="RestoreSettings"></a>
##Scegliere le impostazioni di ripristino del sito Web e avviare l'operazione di ripristino
1. In **Scegliere le impostazioni di ripristino del sito Web**, **Ripristina** selezionare **Sito Web corrente** o **Nuova istanza di sito Web**.
	
	![Choose your web site restore settings][ChooseRestoreSettings]
	
	Se si seleziona **Sito Web corrente**, il sito Web esistente verrà sovrascritto dal backup selezionato (ripristino distruttivo). Tutte le modifiche apportate al sito Web dopo l'esecuzione del backup scelto verranno rimosse in modo permanente e non sarà possibile annullare l'operazione di ripristino. Durante l'operazione di ripristino, il sito Web corrente sarà temporaneamente non disponibile e verrà visualizzato un avviso in tal senso.
	
	Se si seleziona **Nuova istanza di sito Web**, nella stessa area verrà creato un nuovo sito Web con il nome specificato (per impostazione predefinita, il nuovo nome è **restored-***NomeSitoWebPrecedente*). 
	
	Il sito ripristinato sarà caratterizzato dalla stessa configurazione e dagli stessi contenuti impostati nel portale per il sito originale. Includerà inoltre qualsiasi database specificato nel passaggio successivo.
2. Per ripristinare un database insieme al sito Web, in **Database inclusi** selezionare il nome del server di database in cui eseguire il ripristino di tale database nell'elenco a discesa **Ripristina**. È inoltre possibile creare un nuovo server di database in cui eseguire il ripristino oppure scegliere l'impostazione predefinita **Non ripristinare** per non ripristinare il database. 
	
	Dopo aver scelto il nome del server, specificare il nome del database di destinazione per il ripristino nella casella **Nome database**.
	
	Se il ripristino include uno o più database, è possibile selezionare **Regola automaticamente stringhe di connessione** per aggiornare le stringhe di connessione archiviate nel backup in modo che puntino al nuovo database o server di database, a seconda dei casi. Dopo il completamento del ripristino, è consigliabile verificare che tutte le funzionalità correlate ai database funzionino come previsto.
	
	![Choose database server host][ChooseDBServer]
	
	> [AZURE.NOTE] Non è possibile ripristinare un database SQL con lo stesso nome nella stessa istanza di SQL Server. È necessario scegliere un nome di database diverso oppure un host SQL Server diverso in cui ripristinare il database. 
	
	> [AZURE.NOTE] È possibile ripristinare un database MySQL con lo stesso nome nello stesso server. Si noti, tuttavia, che questa operazione comporta la cancellazione di tutti i contenuti esistenti archiviati nel database MySQL.	
	
3. Se si sceglie di ripristinare un database esistente, sarà necessario specificare un nome utente e una password. Se si sceglie di ripristinare in un nuovo database, sarà necessario specificare un nuovo nome di database:
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	Fare clic sulla freccia destra per continuare.	
4. Se si è scelto di creare un nuovo database, nella finestra di dialogo successiva sarà necessario specificare le credenziali e altre informazioni di configurazione iniziali per il database. In questo esempio viene visualizzata la scelta di un nuovo database SQL. Le opzioni per un nuovo database MySQL sono leggermente differenti.
	
	![New SQL database settings][NewSQLDBConfig]
	
5. Fare clic sul segno di spunta per avviare l'operazione di ripristino. Al termine, la nuova istanza di sito Web (se si tratta dell'opzione di ripristino selezionata) sarà visibile nell'elenco dei siti Web nel portale.
	
	![Restored Contoso web site][RestoredContosoWebSite]

<a name="OperationLogs"></a>
##Visualizzare i registri operazioni
	
1. Per visualizzare informazioni dettagliate sull'esito dell'operazione di ripristino del sito Web, passare alla scheda Dashboard del sito Web. Nella sezione **Riepilogo rapido**, in **Servizi di gestione**, fare clic su **Registri operazioni**.
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. Viene visualizzata la pagina **Registri operazioni** del portale dei servizi di gestione, in cui è possibile visualizzare il registro dell'operazione di ripristino nell'elenco dei registri delle operazioni:
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. Per visualizzare informazioni dettagliate sull'operazione, selezionarla nell'elenco e quindi fare clic sul pulsante **Dettagli** nella barra dei comandi.
	
	![Details Button][DetailsButton]
	
	Viene visualizzata la finestra **Dettagli operazione**, con il contenuto copiabile del file di registro:
	
	![Operation Details][OperationDetails]
	

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png


<!--HONumber=42-->
