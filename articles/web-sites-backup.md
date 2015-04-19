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


La funzionalità di backup e ripristino nel [servizio app per app Web di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) consente di creare facilmente backup di app Web in modo manuale o automatico. È possibile ripristinare lo stato precedente dell'app Web oppure creare una nuova app Web basata su uno dei backup dell'app originale. 

Per informazioni sul ripristino di un'app Web di Azure dal backup, vedere [Ripristino di un'app Web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Elementi di cui viene eseguito il backup 
Le app Web possono effettuare il backup delle seguenti informazioni:

* Configurazione app Web
* Contenuto file app Web
* Tutti i server SQL o i database MySQL connessi all'app (è possibile scegliere quali includere nel backup)

Il backup di tali informazioni viene effettuato nell'account di archiviazione di Azure e nel contenitore specificato. 

> [AZURE.NOTE] Ciascun backup è una copia offline completa dell'app, non un aggiornamento incrementale.

<a name="requirements"></a>
## Requisiti e restrizioni

* La funzionalità di backup e ripristino richiede che il sito sia in modalità Standard. Per altre informazioni sul ridimensionamento delle app Web per utilizzare la modalità Standard, vedere [Scalare un'app Web nel servizio app di Azure](web-sites-scale.md). Considerare che la modalità Premium consente l'esecuzione di un numero maggiore di backup quotidiani rispetto alla modalità Standard.

* Per usufruire della funzionalità di backup e ripristino è necessario un account di archiviazione di Azure che appartenga alla stessa sottoscrizione dell'app Web da sottoporre a backup. Se ancora non si dispone di un account di archiviazione, è possibile crearne uno facendo clic su **Account di archiviazione** nel pannello **Backup** del [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), quindi scegliere l'**Account di archiviazione** e il **Contenitore** dal pannello **Destinazione**. Per altre informazioni sugli account di archiviazione di Azure, vedere i [collegamenti ](#moreaboutstorage) alla fine dell'articolo.

<a name="manualbackup"></a>
## Creazione di un backup manuale

1. Nel portale di Azure, scegliere l'app Web dal pannello App Web. In questo modo vengono visualizzati i dettagli dell'app Web in un nuovo pannello.
2. Selezionare l'opzione **Impostazioni**. Verrà visualizzato il pannello **Impostazioni** consentendo di modificare l'app Web.
	
	![Backups page][ChooseBackupsPage]

3. Scegliere l'opzione **Backup** nel pannello **Impostazioni**. Viene visualizzato il pannello**Backup**.
	
4. Dal pannello **Backup**, scegliere la destinazione del backup selezionando un **Account di archiviazione** e un **Contenitore**. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app Web da sottoporre a backup.
	
	![Choose storage account][ChooseStorageAccount]
	
5. Nell'opzione **Database inclusi** nel pannello **Backup**, selezionare i database connessi all'app Web (MySQL o di SQL Server) di cui eseguire il backup. 

	> [AZURE.NOTE] 	Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni app Web** del portale.
	
6. Nel pannello **Backup**, selezionare la **Destinazione di backup**. È necessario scegliere un account di archiviazione e contenitore esistenti.
7. Nella barra dei comandi fare clic su **Esegui backup**.
	
	![BackUpNow button][BackUpNow]
	
	Durante il processo di backup verrà visualizzato un messaggio di stato.
	

È possibile eseguire un backup manuale in qualsiasi momento.  

<a name="automatedbackups"></a>
## Configurazione dei backup automatici

1. Nel pannello **Backup**, impostare **Backup pianificato** su ATTIVO.
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. Selezionare l'account di archiviazione in cui eseguire il backup dell'app Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app Web da sottoporre a backup.
	
	![Choose storage account][ChooseStorageAccount]
	
3. Nella casella **Frequenza**, specificare la frequenza di esecuzione dei backup automatici. 
	Il numero di giorni deve essere compreso tra 1 e 90 compresi (da una volta al giorno a una volta ogni 90 giorni).
	
4. Utilizzare l'opzione **Inizio** per specificare la data e l'ora di inizio per i backup automatizzati. 
	
	> [AZURE.NOTE] In Azure gli orari di backup vengono archiviati in formato UTC, ma vengono visualizzati in base all'ora di sistema impostata nel computer utilizzato per visualizzare il portale.
	
5. Nella sezione **Database inclusi**, selezionare i database connessi all'app Web (MySQL o di SQL Server) di cui eseguire il backup. Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni app Web** del portale.
	
	> [AZURE.NOTE] Se si sceglie di includere nel database uno o più database ed è stata specificata una frequenza di esecuzione inferiore a 7 giorni, verrà visualizzato l'avviso che l'esecuzione di backup troppo frequenti può aumentare i costi del database.
	
6. Inoltre, impostare il valore **Conservazione (giorni)** sul numero di giorni di conservazione del backup.
7. Nella barra dei comandi fare clic sul pulsante **Salva** per salvare le modifiche alla configurazione oppure su **Elimina** per non salvarle.
	
	![Save button][SaveIcon]

<a name="aboutbackups"></a>
## Modalità di archiviazione dei backup

Dopo l'esecuzione di uno o più backup, questi saranno visibili nel pannello **Contenitori** dell'**Account di archiviazione**, così come le app Web. Dall'**Account di archiviazione**, ogni backup è costituito da un file .zip che contiene i dati sottoposti a backup e da un file .xml che contiene un manifesto dei contenuti del file .zip. 

I nomi dei file .zip e .xml del backup sono composti dal nome dell'app Web seguito da un carattere di sottolineatura e da un timestamp relativo alla data e ora di esecuzione del backup. Il timestamp contiene la data in formato AAAAMMGG (in cifre senza spazi), più l'ora in formato 24 ore UTC, ad esempio fabrikam_201402152300.zip. È possibile decomprimere i file ed esplorarne il contenuto, nel caso in cui si desideri accedere ai backup senza effettivamente eseguire un ripristino dell'app Web.

Il file XML archiviato con il file ZIP indica il nome del file di database in  *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Il file di backup del database stesso è archiviato nella radice del file .ZIP. Per un database SQL può essere un file BACPAC (nessuna estensione di file) e può essere importato. Per creare un nuovo database SQL basato sull'esportazione BACPAC, attenersi alla procedura riportata nell'articolo [Importare un file BACPAC per creare un nuovo database utente](http://technet.microsoft.com/library/hh710052.aspx).

Per informazioni sul ripristino di un'app Web (tra cui i database) utilizzando il portale di Azure, vedere [Ripristino di un'app Web nel Servizio app di Azure](web-sites-restore.md).

> [AZURE.NOTE] La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.

<a name="notes"></a>
## Note

* Assicurarsi di configurare correttamente la stringa di connessione di ogni database nel pannello **Impostazioni app Web** in **Impostazioni** dell'app Web in modo che la funzionalità di backup e ripristino possa includere i database.
* Sebbene sia possibile eseguire il backup di più app Web nello stesso account di archiviazione, per facilitare la manutenzione è consigliabile creare un account di archiviazione separato per ogni sito.

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="nextsteps"></a>
## Passaggi successivi
Per informazioni sul ripristino di un'app Web dal backup, vedere [Ripristino di un'app Web nel Servizio app di Azure](web-sites-restore.md).

Per iniziare a usare Azure, vedere [Versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Altre informazioni sugli account di archiviazione

[Informazioni sull'account di archiviazione](storage-whatis-account.md)

[Procedura: Creare un account di archiviazione](storage-create-storage-account.md)

[Come monitorare un account di archiviazione](storage-monitor-storage-account.md)

[Informazioni sulla fatturazione dell'archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=49-->