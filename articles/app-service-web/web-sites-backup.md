<properties 
	pageTitle="Eseguire il backup di un'app Web nel servizio app di Azure" 
	description="Informazioni sulla creazione dei backup delle app Web nel servizio app di Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Eseguire il backup di un'app Web nel servizio app di Azure


La funzionalità di backup e ripristino in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) consente di creare facilmente backup di app Web in modo manuale o automatico. È possibile ripristinare lo stato precedente dell'app Web oppure creare una nuova app Web basata su uno dei backup dell'app originale.

Per informazioni sul ripristino di un'app Web dal backup, vedere [Ripristino di un'app Web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Elementi di cui viene eseguito il backup 
Le app Web possono effettuare il backup delle seguenti informazioni:

* Configurazione app Web
* Contenuto file app Web
* Tutti i server SQL o i database MySQL connessi all'app (è possibile scegliere quali includere nel backup)

Il backup di tali informazioni viene effettuato nell'account di archiviazione di Azure e nel contenitore specificato.

> [AZURE.NOTE]Ciascun backup è una copia offline completa dell'app, non un aggiornamento incrementale.

<a name="requirements"></a>
## Requisiti e restrizioni

* La funzionalità di backup e ripristino richiede che il sito sia in modalità Standard. Per ulteriori informazioni sul ridimensionamento delle app Web per utilizzare la modalità Standard, vedere [Scalare un'app Web nel servizio app di Azure](web-sites-scale.md). Considerare che la modalità Premium consente l'esecuzione di un numero maggiore di backup quotidiani rispetto alla modalità Standard.

* Per usufruire della funzionalità di backup e ripristino è necessario un account di archiviazione di Azure che appartenga alla stessa sottoscrizione dell'app Web da sottoporre a backup. Se ancora non si dispone di un account di archiviazione, è possibile crearne uno facendo clic su **Account di archiviazione** nel pannello **Backup** del [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), quindi scegliere l'**Account di archiviazione** e il **Contenitore** dal pannello **Destinazione**. Per altre informazioni sugli account di archiviazione di Azure, vedere i [collegamenti](#moreaboutstorage) al termine di questo articolo.

* La funzionalità di Backup e ripristino supporta fino a 10 GB di contenuto del sito Web e database. Se non è possibile proseguire a utilizzare la funzionalità di backup perché il payload supera questo limite, verrà riportato un errore nei log operazioni.

<a name="manualbackup"></a>
## Creazione di un backup manuale

1. Nel portale di Azure, scegliere l'app Web dal pannello App Web. In questo modo vengono visualizzati i dettagli dell'app Web in un nuovo pannello.
2. Selezionare l'opzione **Impostazioni**. Verrà visualizzato il pannello **Impostazioni** consentendo di modificare l'app Web.
	
	![Pagina Backups][ChooseBackupsPage]

3. Scegliere l'opzione **Backup** nel pannello **Impostazioni**. Viene visualizzato il pannello **Backup**.
	
4. Dal pannello **Backup**, scegliere la destinazione del backup selezionando un **Account di archiviazione** e un **Contenitore**. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app Web da sottoporre a backup.
	
	![Scelta dell'account di archiviazione][ChooseStorageAccount]
	
5. Nell'opzione **Database inclusi** nel pannello **Backup**, selezionare i database connessi all'app Web (MySQL o SQL Server) di cui eseguire il backup.

	> [AZURE.NOTE]Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni app Web** del portale.
	
6. Nel pannello **Backup**, selezionare la **Destinazione di backup**. È necessario scegliere un account di archiviazione e contenitore esistenti.
7. Nella barra dei comandi fare clic su **Backup Now**.
	
	![Pulsante BackUp Now][BackUpNow]
	
	Durante il processo di backup verrà visualizzato un messaggio di stato.
	

È possibile eseguire un backup manuale in qualsiasi momento.

<a name="automatedbackups"></a>
## Configurazione dei backup automatici

1. Nel pannello **Backup**, impostare **Backup pianificato** su ATTIVO.
	
	![Abilitazione dei backup automatici][SetAutomatedBackupOn]
	
2. Selezionare l'account di archiviazione in cui eseguire il backup dell'app Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app Web da sottoporre a backup.
	
	![Scelta dell'account di archiviazione][ChooseStorageAccount]
	
3. Nella casella **Frequency** specificare la frequenza di esecuzione dei backup automatici. Il numero di giorni deve essere compreso tra 1 e 90 inclusi (da una volta al giorno a una volta ogni 90 giorni).
	
4. Utilizzare l'opzione **Inizio** per specificare la data e l'ora di inizio per i backup automatizzati.
	
	> [AZURE.NOTE]In Azure gli orari di backup vengono archiviati in formato UTC, ma vengono visualizzati in base all'ora di sistema impostata nel computer utilizzato per visualizzare il portale.
	
5. Nella sezione **Database inclusi**, selezionare i database connessi all'app Web (MySQL o di SQL Server) di cui eseguire il backup. Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni app Web** del portale.
	
	> [AZURE.NOTE]Se si sceglie di includere nel database uno o più database ed è stata specificata una frequenza di esecuzione inferiore a 7 giorni, verrà visualizzato l'avviso che l'esecuzione di backup troppo frequenti può aumentare i costi del database.
	
6. Inoltre, impostare il valore **Conservazione (giorni)** sul numero di giorni di conservazione del backup.
7. Nella barra dei comandi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione oppure su **Discard** per non salvarle.
	
	![pulsante Salva][SaveIcon]

<a name="notes"></a>
## Note

* Assicurarsi di configurare correttamente le stringhe di connessione di ogni database nel pannello **Impostazioni app Web** in **Impostazioni** dell'app Web in modo che la funzionalità di backup e ripristino possa includere i database.
* Sebbene sia possibile eseguire il backup di più app Web nello stesso account di archiviazione, per facilitare la manutenzione è consigliabile creare un account di archiviazione separato per ogni sito.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="partialbackups"></a>
## Eseguire il backup solo di una parte del sito

A volte non si desidera eseguire il backup di tutto il contenuto del sito, soprattutto se si esegue regolarmente il backup del sito o se il contenuto del sito supera i 10 GB, ovvero la quantità massima di dati di cui è possibile eseguire il backup in una volta.

Ad esempio, probabilmente non è necessario eseguire il backup dei file di log, oppure, se si [impostano backup settimanali](https://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/#configure-automated-backups), non è necessario riempire l'account di archiviazione con contenuto statico che non viene mai modificato, come vecchi post di blog o immagini.

I backup parziali consentono di scegliere esattamente i file di cui si desidera eseguire il backup.

###Specificare i file di cui non si desidera eseguire il backup
È possibile creare un elenco di file e cartelle da escludere dal backup.

Salvare l'elenco come un file di testo denominato _backup.filter nella cartella wwwroot del sito. È possibile eseguire facilmente l'accesso attraverso la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) disponibile all'indirizzo `http://{yoursite}.scm.azurewebsites.net/DebugConsole`. 

Nelle istruzioni seguenti la console Kudu verrà usata per creare il file _backup.filter, ma per inserire il file nel percorso specificato è possibile usare il metodo di distribuzione desiderato.

###Operazioni da eseguire
Si supponga di avere un sito che contiene file di log e immagini statiche degli anni precedenti che non verranno mai modificati

e di disporre già di un backup completo del sito che include le vecchie immagini. Si desidera ora eseguire il backup del sito ogni giorno, senza però dover pagare per l'archiviazione dei file di log o per i file di immagini statiche che non vengono mai modificati.

![Cartella dei log][LogsFolder] ![Cartella delle immagini][ImagesFolder]
	
I passaggi seguenti illustrano come escludere tali file dal backup.

####Identificare i file e le cartelle di cui non si desidera eseguire il backup
L'operazione è semplice. Se si sa già che non si desidera eseguire il backup dei file di log, escludere `D:\home\site\wwwroot\Logs`.

Esiste un'altra cartella di file di log che per tutte le app Web di Azure si trova nel percorso `D:\home\LogFiles`. Escludere anche questa cartella.

Se inoltre non si desidera eseguire ripetutamente il backup delle immagini degli anni precedenti, aggiungere anche `D:\home\site\wwwroot\Images\2013` e `D:\home\site\wwwroot\Images\2014` all'elenco.

Infine, verrà escluso dal backup anche il file brand.png nella cartella Immagini, al fine di mostrare che è possibile disattivare il backup anche di singoli file. Il file si trova nel percorso `D:\home\site\wwwroot\Images\brand.png`

In questo modo le cartelle seguenti verranno escluse dal backup:

* D:\home\site\wwwroot\Logs
* D:\home\LogFiles
* D:\home\site\wwwroot\Images\2013
* D:\home\site\wwwroot\Images\2014
* D:\home\site\wwwroot\Images\brand.png

#### Creare l'elenco di esclusione
Salvare la black list dei file e delle cartelle di cui non si desidera eseguire il backup in un file speciale denominato _backup.filter. Creare il file e inserirlo nel percorso `D:\home\site\wwwroot_backup.filter`.

Elencare tutti i file e le cartelle di cui non si desidera eseguire il backup nel file _backup.filter. Aggiungere il percorso completo relativo a D:\home della cartella o del file che si desidera escludere dal backup, inserendo un percorso per riga.

Per il sito considerato `D:\home\site\wwwroot\Logs` diventa quindi `\site\wwwroot\Logs`, `D:\home\LogFiles` diventa `\LogFiles` e così via, generando il contenuto seguente per il file _backup.filter:

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

Si noti il carattere `` all'inizio di ogni riga. È importante che quel carattere sia presente.

###Eseguire un backup
È ora possibile eseguire i backup nello stesso modo in cui si eseguono normalmente. È possibile eseguire un backup [manuale](https://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/#create-a-manual-backup) o [automatico](https://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/#configure-automated-backups).

I file e le cartelle che rientrano nei filtri elencati nel file _backup.filter verranno esclusi dal backup. Pertanto i file di log e i file con le immagini del 2013 e del 2014 non verranno più sottoposti a backup.

###Ripristino del backup del sito
È possibile ripristinare i backup parziali del sito nello stesso modo in cui si [ripristina un backup regolare](https://azure.microsoft.com/it-it/documentation/articles/web-sites-restore/). L'operazione di ripristino verrà eseguita correttamente.

####Dettagli tecnici
Nei backup completi (non parziali) in genere tutto il contenuto del sito viene sostituito con il contenuto del backup. Se un file è presente nel sito ma non nel backup, verrà eliminato.

Quando invece si ripristinano backup parziali, il contenuto che si trova in una delle cartelle nella black list (ad esempio, `D:\home\site\wwwroot\images\2014` per il sito considerato) verrà lasciato invariato. Se nella black list erano presenti singoli file, durante il ripristino verranno anch'essi lasciati invariati.

<a name="aboutbackups"></a>
## Modalità di archiviazione dei backup

Dopo l'esecuzione di uno o più backup, questi saranno visibili nel pannello **Contenitori** dell'**Account di archiviazione**, così come le app Web. Dall' **Account di archiviazione**, ogni backup è costituito da un file .zip che contiene i dati sottoposti a backup e da un file .xml che contiene un manifesto dei contenuti del file .zip.

I nomi dei file .zip e .xml del backup sono composti dal nome dell'app Web seguito da un carattere di sottolineatura e da un timestamp relativo alla data e ora di esecuzione del backup. Il timestamp contiene la data in formato AAAAMMGG (in cifre senza spazi), più l'ora in formato 24 ore UTC, ad esempio fabrikam_201402152300.zip. È possibile decomprimere i file ed esplorarne il contenuto, nel caso in cui si desideri accedere ai backup senza effettivamente eseguire un ripristino dell'app Web.

Il file XML archiviato con il file ZIP indica il nome del file di database in *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Il file di backup del database stesso è archiviato nella radice del file .ZIP. Per un database SQL può essere un file BACPAC (nessuna estensione di file) e può essere importato. Per creare un nuovo database SQL basato sull'esportazione BACPAC attenersi alla procedura riportata nell'articolo [Importare un file BACPAC per creare un nuovo database utente](http://technet.microsoft.com/library/hh710052.aspx).

Per informazioni sul ripristino di un'app Web (tra cui i database) utilizzando il portale di Azure, vedere [Ripristino di un'app Web nel servizio app di Azure](web-sites-restore.md).

> [AZURE.NOTE]La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.

<a name="bestpractices"></a>
##Procedure consigliate
Cosa occorre fare per ripristinare il sito in caso di emergenza? Assicurarsi di essere preparati per questa eventualità.

È possibile eseguire backup parziali, ma è necessario eseguire innanzitutto almeno un backup completo del sito, in modo da disporre di una copia di tutti i contenuti del sito (questa è la pianificazione nel caso dello scenario peggiore). Quando si ripristinano i backup, è possibile ripristinare prima il backup completo del sito e quindi il backup parziale più recente.

In questo modo è possibile usare gli [slot di distribuzione](https://azure.microsoft.com/it-it/documentation/articles/web-sites-staged-publishing/) per testare il sito ripristinato. È anche possibile testare il processo di ripristino senza modificare il sito di produzione. Il test del processo di ripristino è un'[operazione molto utile](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Si potrebbe verificare un problema come quello descritto di seguito, in cui durante un tentativo di ripristino di un blog, la metà del contenuto è andato perduto.

###Un'esperienza da evitare

Il blog si basa sulla piattaforma di blog [Ghost](https://ghost.org/). Lo sviluppatore responsabile aveva correttamente creato una copia di backup del sito. Un giorno è stato visualizzato un messaggio che indicava che era disponibile una nuova versione di Ghost ed era possibile aggiornare il blog. L'installazione è riuscita.

È stato eseguito un altro backup del sito per includere nel backup i post di blog più recenti e si è quindi proceduto all'aggiornamento di Ghost

nel sito di produzione.

È stato un grave errore.

Si è verificato un errore con l'aggiornamento e la home page mostrava una schermata vuota. Si pensava fosse sufficiente ripristinare il backup appena eseguito

ma, dopo aver ripristinato l'aggiornamento, è stato ripristinato tutto il contenuto ad eccezione dei post di blog.

Com'è stato possibile?

Nelle [note relative all'aggiornamento di Ghost](http://support.ghost.org/how-to-upgrade/) è infatti presente questo avviso:

![È possibile eseguire una copia del database del contenuto e dei dati, ma è consigliabile non farlo durante l'esecuzione di Ghost. È necessario arrestare prima Ghost.][GhostUpgradeWarning]

Se si tenta di eseguire il backup dei dati durante l'esecuzione di Ghost, i dati in realtà non vengono salvati.

Un vero peccato.

Se si fosse prima tentato un ripristino di uno slot di test, sarebbe emerso il problema e i post non sarebbero andati perduti.

Purtroppo è andata così, ma [qualsiasi utente](http://blog.codinghorror.com/international-backup-awareness-day/) può incorrere nello stesso problema.

È consigliabile pertanto eseguire il test dei backup.

<a name="nextsteps"></a>
## Passaggi successivi
Per informazioni sul ripristino di un'app Web dal backup, vedere [Ripristinare un'app Web nel servizio app di Azure](web-sites-restore.md).

Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Ulteriori informazioni sugli account di archiviazione

[Informazioni sull'account di archiviazione](../storage-whatis-account.md)

[Come creare un account di archiviazione](../storage-create-storage-account/)

[Come monitorare un account di archiviazione](../storage-monitor-storage-account.md)

[Informazioni sulla fatturazione di archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=62-->