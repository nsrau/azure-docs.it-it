<properties linkid="web-sites-backup" urlDisplayName="Azure Web Sites Backups" pageTitle="Azure Web Sites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure web sites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Web Sites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

Backup di Siti Web di Azure
===========================

La funzionalità di backup e ripristino di Siti Web di Azure consente di creare con facilità backup dei siti Web in modalità manuale o automatica. È possibile ripristinare lo stato precedente di un sito Web oppure creare un nuovo sito Web basato su uno dei backup del sito originale.

Per informazioni dettagliate sul ripristino di un sito Web dal backup, vedere [Ripristino di un sito Web di Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

Contenuto dell'articolo
-----------------------

-   [Elementi di cui viene eseguito il backup](#whatsbackedup)
-   [Requisiti e restrizioni](#requirements)
-   [Per creare un backup manuale](#manualbackup)
-   [Per configurare backup automatici](#automatedbackups)
-   [Modalità di archiviazione dei backup](#aboutbackups)
-   [Note](#notes)
-   [Passaggi successivi](#nextsteps)
    -   [Ulteriori informazioni sugli account di archiviazione](#moreaboutstorage)

## Elementi di cui viene eseguito il backup In Siti Web di Azure viene eseguito il backup delle informazioni seguenti:

-   Configurazione del sito Web
-   Contenuto dei file del sito Web
-   Qualsiasi database MySQL o di SQL Server connesso al sito (è possibile scegliere i database da includere nel backup)

Il backup delle informazioni viene eseguito nell'account di archiviazione di Azure specificato.

> [WACOM.NOTE] Ogni backup è una copia offline completa del sito Web, non un aggiornamento incrementale.

## Requisiti e restrizioni

-   Per usufruire della funzionalità di backup e ripristino è necessario che il sito sia incluso in un livello Standard. Per ulteriori informazioni su come scalare il sito Web per passare a un livello Standard, vedere [Come applicare la scalabilità ai siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-scale/).

-   Per usufruire della funzionalità di backup e ripristino è necessario un account di archiviazione di Azure che appartenga alla stessa sottoscrizione del sito Web da sottoporre a backup. Se non si dispone ancora di un account di archiviazione, è possibile crearne uno selezionando l'icona **Storage** (griglia) nel riquadro sinistro del portale di Azure, quindi scegliere **New** sulla barra dei comandi nella parte inferiore. Per ulteriori informazioni sugli account di archiviazione di Azure, vedere i [collegamenti](#moreaboutstorage) al termine di questo articolo.

## Per creare un backup manuale

1.  Nel portale di Azure per il sito Web passare alla scheda **Backups**.

    ![Pagina Backups](./media/web-sites-backup/01ChooseBackupsPage.png)

2.  Selezionare l'account di archiviazione in cui eseguire il backup del sito Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione del sito Web da sottoporre a backup.

    ![Scelta dell'account di archiviazione](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Nella sezione **Included Databases** selezionare i database connessi al sito Web (MySQL o di SQL Server) di cui eseguire il backup.

    ![Scelta dei database da includere](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Per visualizzare un database in questo elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Connection Strings** della scheda Configure del portale.

4.  Nella barra dei comandi fare clic su **Backup Now**.

    ![Pulsante BackUp Now](./media/web-sites-backup/04BackUpNow.png)

    Durante il processo di backup verrà visualizzato un messaggio di stato:

    ![Messaggio sullo stato di avanzamento del backup](./media/web-sites-backup/05BackupProgress.png)

È possibile eseguire un backup manuale in qualsiasi momento. Durante l'anteprima non è possibile eseguire più di 2 backup manuali in un periodo di 24 ore (soggetto a modifiche).

## Per configurare backup automatici

1.  Nella pagina Backups impostare **Automated Backup** su ON.

    ![Abilitazione dei backup automatici](./media/web-sites-backup/06SetAutomatedBackupOn.png)

2.  Selezionare l'account di archiviazione in cui eseguire il backup del sito Web. L'account di archiviazione deve appartenere alla stessa sottoscrizione del sito Web da sottoporre a backup.

    ![Scelta dell'account di archiviazione](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Nella casella **Frequency** specificare la frequenza di esecuzione dei backup automatici. (Durante l'anteprima, l'unica unità di tempo disponibile sono i giorni.)

    ![Scelta della frequenza dei backup](./media/web-sites-backup/07Frequency.png)

    Il numero di giorni deve essere compreso tra 1 e 90 inclusi (da una volta al giorno a una volta ogni 90 giorni).

4.  Utilizzare l'opzione **Start Date** per specificare la data e l'ora di inizio per i backup automatizzati.

    ![Scelta della data di inizio](./media/web-sites-backup/08StartDate.png)

    Le ore sono disponibili in incrementi di mezz'ora.

    ![Scelta dell'ora di inizio](./media/web-sites-backup/09StartTime.png)

    > [WACOM.NOTE] In Azure gli orari di backup vengono archiviati in formato UTC, ma vengono visualizzati in base all'ora di sistema impostata nel computer utilizzato per visualizzare il portale.

5.  Nella sezione **Included Databases** selezionare i database connessi al sito Web (MySQL o di SQL Server) di cui eseguire il backup. Per visualizzare un database nell'elenco è necessaria la presenza della relativa stringa di connessione nella sezione **Connection Strings** della scheda Configure del portale.

    ![Scelta dei database da includere](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Se si sceglie di includere nel database uno o più database ed è stata specificata una frequenza di esecuzione inferiore a 7 giorni, verrà visualizzato l'avviso che l'esecuzione di backup troppo frequenti può aumentare i costi del database.

6.  Nella barra dei comandi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione oppure su **Discard** per non salvarle.

    ![Pulsante Salva](./media/web-sites-backup/10SaveIcon.png)

## Modalità di archiviazione dei backup

Dopo l'esecuzione di uno o più backup, questi saranno visibili nella scheda Containers dell'account di archiviazione. I backup saranno archiviati in un contenitore denominato **websitebackups**. Ogni backup è costituito da un file ZIP che contiene i dati sottoposti a backup e da un file XML che contiene un manifesto dei contenuti del file ZIP.

I nomi dei file ZIP e XML del backup sono composti dal nome del sito Web seguito da un carattere di sottolineatura e da un timestamp relativo alla data e ora di esecuzione del backup. Il timestamp contiene la data in formato AAAAMMGG (in cifre senza spazi), più l'ora in formato 24 ore UTC, ad esempio fabrikam\_201402152300.zip. È possibile decomprimere i file ed esplorarne il contenuto, nel caso in cui si desideri accedere ai backup senza effettivamente eseguire un ripristino del sito Web.

> [WACOM.NOTE] La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.

## Note

-   Assicurarsi di configurare correttamente la stringa di connessione di ogni database nella scheda Configure del sito Web, in modo che la funzionalità di backup e ripristino possa includere i database.
-   Durante l'anteprima l'utente è responsabile della gestione dei contenuti sottoposti a backup salvati nell'account di archiviazione. Se si elimina un backup dall'account di archiviazione senza averne effettuata una copia in una posizione diversa, non sarà possibile eseguire il ripristino in un momento successivo.
-   Sebbene sia possibile eseguire il backup di più siti Web nello stesso account di archiviazione, per facilitare la manutenzione è consigliabile creare un account di archiviazione separato per ogni sito.
-   Durante l'anteprima le operazioni di backup e ripristino sono disponibili solo tramite il portale di gestione di Azure.

## Passaggi successivi 
Per informazioni sul ripristino di un sito Web di Azure dal backup, vedere [Ripristino di un sito Web di Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

### Ulteriori informazioni sugli account di archiviazione

[Informazioni sull'account di archiviazione](http://www.windowsazure.com/it-it/documentation/articles/storage-whatis-account/)

[Procedura: Creare un account di archiviazione](http://www.windowsazure.com/it-it/documentation/articles/storage-create-storage-account/)

[Come monitorare un account di archiviazione](http://www.windowsazure.com/it-it/documentation/articles/storage-monitor-storage-account/)

[Informazioni sulla fatturazione dell'archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

