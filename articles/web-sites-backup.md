<properties 
	pageTitle="Backup di Siti Web di Azure" 
	description="Informazioni su come creare backup dei siti Web di Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Backup di Siti Web di Azure

La funzionalità di backup e ripristino di Siti Web di Azure consente di creare con facilità backup dei siti Web in modalità manuale o automatica. È possibile ripristinare lo stato precedente di un sito Web oppure creare un nuovo sito Web basato su uno dei backup del sito originale. 


Per informazioni sul ripristino di un sito Web di Azure dal backup, vedere [Ripristinare un sito Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

##Contenuto dell'articolo

- [Backup semplice e automatico (video)](#video)
- [Elementi di cui viene eseguito il backup](#whatsbackedup)
- [Requisiti e restrizioni](#requirements)
- [Per creare un backup manuale](#manualbackup)
- [Per configurare backup automatici](#automatedbackups)
- [Modalità di archiviazione dei backup](#aboutbackups)
- [Note](#notes)
- [Passaggi successivi](#nextsteps)
	- [Altre informazioni sugli account di archiviazione](#moreaboutstorage)

<a name="video"></a>
##Backup semplice e automatico (video)

In questo video Eduardo Laureano e Scott Hanselman presentano i backup di Siti Web di Azure. (Durata: 11:43)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##Elementi di cui viene eseguito il backup 
In Siti Web di Azure viene eseguito il backup delle informazioni seguenti:

* Configurazione dei siti Web
* Contenuto dei file dei siti Web
* Qualsiasi database MySQL o di SQL Server connesso al sito (è possibile scegliere i database da includere nel backup)

Il backup delle informazioni viene eseguito nell'account di archiviazione di Azure specificato. 

> [AZURE.NOTE] Ogni backup è una copia offline completa del sito Web, non un aggiornamento incrementale.

<a name="requirements"></a>
##Requisiti e restrizioni

* Per usufruire della funzionalità di backup e ripristino è necessario che il sito si trovi in un livello Standard. Per altre informazioni su come scalare il sito Web per passare a un livello Standard, vedere [Come scalare siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-scale/). 

* Per usufruire della funzionalità di backup e ripristino è necessario un account di archiviazione di Azure che appartenga alla stessa sottoscrizione del sito Web di cui eseguire il backup. Se non si dispone ancora di un account di archiviazione, è possibile crearne uno facendo clic sul pulsante **Archiviazione** (icona griglia) nel riquadro sinistro del portale Azure, quindi scegliere **Nuovo** sulla barra dei comandi nella parte inferiore. Per altre informazioni sugli account di archiviazione di Azure, vedere i [collegamenti](#moreaboutstorage) alla fine di questo articolo.

<a name="manualbackup"></a>
## Per creare un backup manuale

1. Nel portale Azure per il sito Web fare clic sulla scheda **Backup**.
	
	![Pagina Backup][ChooseBackupsPage]
	
2. Selezionare l'account di archiviazione in cui eseguire il backup del sito Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione del sito Web da sottoporre a backup.
	
	![Scelta di un account di archiviazione][ChooseStorageAccount]
	
3. Nell'opzione **Database inclusi** selezionare i database connessi al sito Web (SQL Server o MySQL) di cui eseguire il backup. 
	
	![Scelta dei database da includere][IncludedDatabases]

	> [AZURE.NOTE] 	Per visualizzare un database in questo elenco, è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** della scheda Configura del portale.
	
4. Nella barra dei comandi fare clic su **Esegui backup ora**.
	
	![Pulsante Esegui backup ora][BackUpNow]
	
	Durante il processo di backup verrà visualizzato un messaggio di stato:
	
	![Messaggio di stato del backup][BackupProgress]
	
È possibile eseguire un backup manuale in qualsiasi momento. Durante l'anteprima non è possibile eseguire più di 2 backup manuali in un periodo di 24 ore (soggetto a modifiche).  

<a name="automatedbackups"></a>
## Per configurare backup automatici

1. Nella pagina Backup impostare **Backup automatizzato** su ATTIVATO.
	
	![Abilitazione dei backup automatizzati][SetAutomatedBackupOn]
	
2. Selezionare l'account di archiviazione in cui eseguire il backup del sito Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione del sito Web da sottoporre a backup.
	
	![Scelta di un account di archiviazione][ChooseStorageAccount]
	
3. Nella casella **Frequenza** specificare la frequenza di esecuzione dei backup automatizzati. (Durante l'anteprima, l'unica unità di tempo disponibile sono i giorni.)
	
	![Scelta della frequenza dei backup][Frequency]
	
	Il numero di giorni deve essere compreso tra 1 e 90 inclusi (da una volta al giorno a una volta ogni 90 giorni).
	
4. Usare l'opzione **Data inizio** per specificare la data e l'ora di inizio per i backup automatizzati. 
	
	![Scelta di una data di inizio][StartDate]
	
	Le ore sono disponibili in incrementi di mezz'ora.
	
	![Scelta di un'ora di inizio][StartTime]
	
	> [AZURE.NOTE] In Azure gli orari di backup vengono archiviati in formato UTC, ma vengono visualizzati in base all'ora di sistema impostata nel computer usato per visualizzare il portale.
	
5. Nella sezione **Database inclusi** selezionare i database connessi al sito Web (SQL Server o MySQL) di cui eseguire il backup. Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** della scheda Configura del portale.
	
	![Scelta dei database da includere][IncludedDatabases]
	
	> [AZURE.NOTE] Se si sceglie di includere nel backup uno o più database ed è stata specificata una frequenza di esecuzione inferiore a 7 giorni, verrà visualizzato l'avviso che l'esecuzione di backup troppo frequenti può aumentare i costi del database.
	
6. Nella barra dei comandi fare clic sul pulsante **Salva** per salvare le modifiche alla configurazione oppure su **Ignora** per non salvarle.
	
	![Pulsante Salva][SaveIcon]

<a name="aboutbackups"></a>
## Modalità di archiviazione dei backup

Dopo l'esecuzione di uno o più backup, questi saranno visibili nella scheda Containers dell'account di archiviazione. I backup saranno archiviati in un contenitore denominato **websitebackups**. Ogni backup è costituito da un file ZIP che contiene i dati sottoposti a backup e da un file XML che contiene un manifesto dei contenuti del file ZIP. 

I nomi dei file ZIP e XML del backup sono composti dal nome del sito Web seguito da un carattere di sottolineatura e da un timestamp relativo alla data e all'ora di esecuzione del backup. Il timestamp contiene la data in formato AAAAMMGG (in cifre senza spazi), più l'ora in formato 24 ore UTC, ad esempio fabrikam_201402152300.zip. È possibile decomprimere i file ed esplorarne il contenuto, nel caso in cui si voglia accedere ai backup senza effettivamente eseguire un ripristino del sito Web.

Il file XML archiviato con il file ZIP indica il nome del file di database in *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Il file di backup del database stesso è archiviato nella radice del file .ZIP. Per un database SQL può essere un file BACPAC (nessuna estensione di file) e può essere importato. Per creare un nuovo database SQL basato sull'esportazione BACPAC, attenersi alla procedura riportata nell'articolo relativo all'[importazione di un file BACPAC per creare un nuovo database utente](http://technet.microsoft.com/it-it/library/hh710052.aspx).

Per informazioni sul ripristino di un sito Web di Azure (inclusi i database) mediante il portale di gestione di Azure, vedere [Ripristinare un sito Web di Microsoft Azure]( http://azure.microsoft.com/it-it/documentation/articles/web-sites-restore/).

> [AZURE.NOTE] La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.

<a name="notes"></a>
## Note

* Assicurarsi di configurare correttamente la stringa di connessione di ogni database nella scheda Configura del sito Web, in modo che la funzionalità di backup e ripristino possa includere i database.
* Durante l'anteprima l'utente è responsabile della gestione dei contenuti sottoposti a backup salvati nell'account di archiviazione. Se si elimina un backup dall'account di archiviazione senza averne effettuata una copia in una posizione diversa, non sarà possibile eseguire il ripristino in un momento successivo. 
* Sebbene sia possibile eseguire il backup di più siti Web nello stesso account di archiviazione, per facilitare la manutenzione è consigliabile creare un account di archiviazione separato per ogni sito.
* Durante l'anteprima le operazioni di backup e ripristino sono disponibili solo tramite il portale di gestione di Azure.

<a name="nextsteps"></a>
## Passaggi successivi
Per informazioni sul ripristino di un sito Web di Azure dal backup, vedere [Ripristinare un sito Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](http://azure.microsoft.com/it-it/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Altre informazioni sugli account di archiviazione

[Informazioni sugli account di archiviazione di Azure](http://www.windowsazure.com/it-it/documentation/articles/storage-whatis-account/)

[Procedura: Creare un account di archiviazione](http://www.windowsazure.com/it-it/documentation/articles/storage-create-storage-account/)

[Monitorare un account di archiviazione nel portale di gestione di Azure](http://www.windowsazure.com/it-it/documentation/articles/storage-monitor-storage-account/)

[Informazioni sulla fatturazione dell'archiviazione di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

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


<!--HONumber=42-->
