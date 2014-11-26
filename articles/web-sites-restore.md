<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure website" pageTitle="Restore a Microsoft Azure website" metaKeywords="Azure Websites, Restore, restoring" description="Learn how to restore your Azure websites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure website" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Ripristinare un sito Web di Microsoft Azure

In questo articolo viene illustrato come ripristinare un sito Web precedentemente sottoposto a backup mediante la funzionalità di backup di Siti Web di Azure. Per altre informazioni, vedere [Backup di Siti Web di Microsoft Azure][Backup di Siti Web di Microsoft Azure].

La funzionalità di ripristino di Siti Web di Azure consente di ripristinare su richiesta lo stato precedente di un sito Web oppure di creare un nuovo sito Web basato su un backup del sito originale. La creazione di un nuovo sito Web eseguito in parallelo con l'ultima versione può essere utile per l'esecuzione di test A/B.

La funzionalità di ripristino, disponibile nella scheda Backup del portale Siti Web di Azure, è disponibile solo in modalità Standard.

## Contenuto dell'articolo

-   [Per ripristinare un sito Web di Azure da un backup eseguito in precedenza][Per ripristinare un sito Web di Azure da un backup eseguito in precedenza]
-   [Per ripristinare un sito Web di Azure direttamente da un account di archiviazione][Per ripristinare un sito Web di Azure direttamente da un account di archiviazione]
-   [Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino][Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino]
-   [Visualizzazione dei log operazioni][Visualizzazione dei log operazioni]

<a name="PreviousBackup"></a>

## Per ripristinare un sito Web di Azure da un backup eseguito in precedenza

1.  Nella scheda **Backups** fare clic su **Restore Now** nella barra dei comandi nella parte inferiore della pagina del portale. Verrà visualizzata la finestra di dialogo **Restore Now**.

    ![Choose backup source][Choose backup source]

2.  In **Scegliere l'origine del backup**, selezionare **Backup precedente per questo sito Web**.
3.  Selezionare la data del backup da ripristinare, quindi fare clic sulla freccia destra per continuare.
4.  Attenersi alla procedura riportata nella sezione [Scelta delle impostazioni di ripristino del sito Web][Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino] più avanti in questo articolo.

<a name="StorageAccount"></a>

## Per ripristinare un sito Web di Azure direttamente da un account di archiviazione

1.  Nella scheda **Backups** fare clic su **Restore Now** nella barra dei comandi nella parte inferiore della pagina del portale. Verrà visualizzata la finestra di dialogo **Restore Now**.

    ![Choose backup source][Choose backup source]

2.  In **Choose backup source** selezionare **Storage Account File**. Qui è possibile specificare direttamente l'URL del file dell'account di archiviazione oppure fare clic sull'icona della cartella per passare all'archiviazione BLOB e specificare il file di backup. In questo esempio viene selezionata l'icona della cartella.

    ![File dell'account di archiviazione][File dell'account di archiviazione]

3.  Fare clic sull'icona della cartella per aprire la finestra di dialogo **Browse Cloud Storage**.

    ![Browse Cloud Storage][Browse Cloud Storage]

4.  Espandere il nome dell'account di archiviazione da usare e quindi selezionare **websitebackups**, che contiene i backup dell'utente.
5.  Selezionare il file ZIP contenente il backup da ripristinare e quindi fare clic su **Open**.
6.  Il file dell'account di archiviazione verrà selezionato e visualizzato nella casella. Fare clic sulla freccia destra per continuare.

    ![File dell'account di archiviazione selezionato][File dell'account di archiviazione selezionato]

7.  Continuare con la sezione seguente, [Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino][Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino].

<a name="RestoreSettings"></a>

## Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino

1.  In **Scegli le impostazioni di ripristino del sito Web**, **Ripristina** selezionare **Sito Web corrente** o **Nuova istanza sito Web**.

    ![Choose your web site restore settings][Choose your web site restore settings]

    Se si seleziona **Sito Web corrente**, il sito Web esistente verrà sovrascritto dal backup selezionato (ripristino distruttivo). Tutte le modifiche apportate al sito Web dopo l'esecuzione del backup scelto verranno rimosse in modo permanente e non sarà possibile annullare l'operazione di ripristino. Durante l'operazione di ripristino, il sito Web corrente sarà temporaneamente non disponibile e verrà visualizzato un avviso in tal senso.

    Se si seleziona **Nuova istanza sito Web**, nella stessa area verrà creato un nuovo sito Web con il nome specificato (per impostazione predefinita, il nuovo nome è **restored-oldWebSiteName**).

    Il sito ripristinato sarà caratterizzato dalla stessa configurazione e dagli stessi contenuti impostati nel portale per il sito originale. Includerà inoltre qualsiasi database specificato nel passaggio successivo.

2.  Per ripristinare un database insieme al sito Web, in **Database inclusi** selezionare il nome del server di database in cui eseguire il ripristino di tale database nell'elenco a discesa **Ripristina**. È inoltre possibile creare un nuovo server di database in cui eseguire il ripristino oppure scegliere **Don't Restore** per non ripristinare il database, che è l'impostazione predefinita.

    Dopo aver scelto il nome del server, specificare il nome del database di destinazione per il ripristino nella casella **Database Name**.

    Se il ripristino include uno o più database, è possibile selezionare **Automatically adjust connection strings** per aggiornare le stringhe di connessione archiviate nel backup in modo che puntino al nuovo database o server di database, come appropriato. Dopo il completamento del ripristino, è consigliabile verificare che tutte le funzionalità correlate ai database funzionino come previsto.

    ![Scelta dell'host server di database][Scelta dell'host server di database]

    > [WACOM.NOTE] Non è possibile ripristinare un database SQL con lo stesso nome nella stessa istanza di SQL Server. È necessario scegliere un nome di database diverso oppure un host SQL Server diverso in cui ripristinare il database.

    > [WACOM.NOTE] È possibile ripristinare un database MySQL con lo stesso nome nello stesso server. Tenere presente, tuttavia, che questa operazione comporta la cancellazione di tutti i contenuti esistenti archiviati nel database MySQL.

3.  Se si sceglie di ripristinare un database esistente, sarà necessario specificare un nome utente e una password. Se si sceglie di ripristinare in un nuovo database, sarà necessario specificare un nuovo nome di database:

    ![Ripristino in un nuovo database SQL][Ripristino in un nuovo database SQL]

    Fare clic sulla freccia destra per continuare.

4.  Se si è scelto di creare un nuovo database, nella finestra di dialogo successiva sarà necessario specificare le credenziali e altre informazioni di configurazione iniziali per il database. In questo esempio viene visualizzata la scelta di un nuovo database SQL. Le opzioni per un nuovo database MySQL sono leggermente differenti.

    ![Impostazioni del nuovo database SQL][Impostazioni del nuovo database SQL]

5.  Fare clic sul segno di spunta per avviare l'operazione di ripristino. Al termine, la nuova istanza di sito Web (se si tratta dell'opzione di ripristino selezionata) sarà visibile nell'elenco dei siti Web nel portale.

    ![Sito Web Contoso ripristinato][Sito Web Contoso ripristinato]

<a name="OperationLogs"></a>

## Visualizzazione dei log operazioni

1.  Per visualizzare informazioni dettagliate sull'esito dell'operazione di ripristino del sito Web, passare alla scheda Dashboard del sito Web. Nella sezione **Quick Glance**, in **Management Services**, fare clic su **Operation Logs**.

    ![Dashboard - Collegamento Operation Logs][Dashboard - Collegamento Operation Logs]

2.  Verrà visualizzata la pagina **Operation Logs** del portale dei servizi di gestione, in cui è possibile visualizzare il log dell'operazione di ripristino nell'elenco dei log delle operazioni:

    ![Pagina Operation Logs di Management Services][Pagina Operation Logs di Management Services]

3.  Per visualizzare informazioni dettagliate sull'operazione, selezionarla nell'elenco e quindi fare clic sul pulsante **Details** nella barra dei comandi.

    ![Pulsante Details][Pulsante Details]

    Verrà visualizzata la finestra **Operations Details**, con il contenuto copiabile del file di log:

    ![Dettagli operazione][Dettagli operazione]

<!-- IMAGES -->

  [Backup di Siti Web di Microsoft Azure]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-backup/
  [Per ripristinare un sito Web di Azure da un backup eseguito in precedenza]: #PreviousBackup
  [Per ripristinare un sito Web di Azure direttamente da un account di archiviazione]: #StorageAccount
  [Scelta delle impostazioni di ripristino del sito Web e avvio dell'operazione di ripristino]: #RestoreSettings
  [Visualizzazione dei log operazioni]: #OperationLogs
  [Choose backup source]: ./media/web-sites-restore/01ChooseBackupSource.png
  [File dell'account di archiviazione]: ./media/web-sites-restore/02StorageAccountFile.png
  [Browse Cloud Storage]: ./media/web-sites-restore/03BrowseCloudStorage.png
  [File dell'account di archiviazione selezionato]: ./media/web-sites-restore/04StorageAccountFileSelected.png
  [Choose your web site restore settings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
  [Scelta dell'host server di database]: ./media/web-sites-restore/06ChooseDBServer.png
  [Ripristino in un nuovo database SQL]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
  [Impostazioni del nuovo database SQL]: ./media/web-sites-restore/08NewSQLDBConfig.png
  [Sito Web Contoso ripristinato]: ./media/web-sites-restore/09RestoredContosoWebSite.png
  [Dashboard - Collegamento Operation Logs]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
  [Pagina Operation Logs di Management Services]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
  [Pulsante Details]: ./media/web-sites-restore/12DetailsButton.png
  [Dettagli operazione]: ./media/web-sites-restore/13OperationDetails.png
