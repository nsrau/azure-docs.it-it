<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

Servizi BizTalk: Backup e ripristino
====================================

I Servizi BizTalk di Azure includono funzioni di backup e ripristino. Quando si crea un backup, verrà inoltre creato uno snapshot della configurazione dei Servizi BizTalk di Azure.

Esaminare gli scenari seguenti:

-   È possibile eseguire il backup della configurazione del proprio servizio BizTalk tramite il portale di gestione di Azure in due modi: creando i backup in base alle esigenze (backup ad hoc) es eseguendo backup pianificati.

-   È possibile ripristinare il contenuto del backup nello stesso servizio BizTalk oppure in nuovo Servizio BizTalk. Per ripristinare il Servizio BizTalk utilizzando lo stesso nome è necessario eliminare il Servizio BizTalk esistente, altrimenti il ripristino avrà esito negativo.

-   È possibile ripristinare la stessa edizione oppure un'edizione successiva dei Servizi BizTalk. Il ripristino di una versione precedente del Servizio BizTalk, risalente al momento in cui è stato creato il backup, non è supportato.

    Ad esempio, è possibile ripristinare un backup creato con l'edizione Basic nell'edizione Premium, ma non è possibile ripristinare un backup creato con l'edizione Premium nell'edizione Standard.

-   Il backup dei numeri di controllo consente di mantenere la continuità dei numeri di controllo. Se dopo l'ultimo backup si elaborano dei messaggi, il ripristino del contenuto di questo backup può causare la generazione di numeri di controllo duplicati.

-   Le operazioni di backup e ripristino non sono disponibili in BizTalk Services Developer Edition.

In questo argomento viene descritto come eseguire il backup e il ripristino dei Servizi BizTalk tramite il portale di gestione di Azure. È inoltre possibile eseguire il backup dei Servizi BizTalk mediante le API REST. Per ulteriori informazioni a tal proposito, vedere l'articolo relativo alle [API REST dei Servizi BizTalk](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx).

[Elementi di cui viene eseguito il backup](#budata)

[Creazione di un backup](#createbu)

[Ripristino](#restore)

Elementi di cui viene eseguito il backup
----------------------------------------

Viene eseguito il backup degli elementi seguenti:

<table data-morhtml="true" border="1"> 
<tr data-morhtml="true" bgcolor="FAF9F9">
<th data-morhtml="true"> </th>
<th data-morhtml="true">Elementi di backup</th> 
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Portale Servizi BizTalk di Azure</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Configurazione e runtime</td> 
<td data-morhtml="true"><bl data-morhtml="true">
<li data-morhtml="true">Dettagli su partner e profilo</li>
<li data-morhtml="true">Contratti con il partner</li>
<li data-morhtml="true">Assembly personalizzati distribuiti</li>
<li data-morhtml="true">Bridge distribuiti</li>
<li data-morhtml="true">Certificati</li>
<li data-morhtml="true">Trasformazioni distribuite</li>
<li data-morhtml="true">Pipeline</li>
<li data-morhtml="true">Modelli creati e salvati nel portale Servizi BizTalk</li>
<li data-morhtml="true">Mapping X12 ST01 e GS01</li>
<li data-morhtml="true">Numeri di controllo (EDI)</li>
<li data-morhtml="true">Valori MIC del messaggio AS2</li>
</bl></td>
</tr> 
 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Servizio BizTalk di Azure</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Certificato SSL</td> 
<td data-morhtml="true">
<bl data-morhtml="true">
<li data-morhtml="true">Dati certificato SSL</li>
<li data-morhtml="true">Password certificato SSL</li>
</bl>
</td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Impostazioni del servizio BizTalk</td> 
<td data-morhtml="true">
<bl data-morhtml="true">
<li data-morhtml="true">Conteggio dell'unit&agrave; di scala</li>
<li data-morhtml="true">Edizione</li>
<li data-morhtml="true">Versione prodotto</li>
<li data-morhtml="true">Area/data center</li>
<li data-morhtml="true">Spazio dei nomi e chiave del Servizio di controllo di accesso (ACS)</li>
<li data-morhtml="true">Rilevamento della stringa di connessione al database</li>
<li data-morhtml="true">Archiviazione della stringa di connessione all'account di archiviazione</li>
<li data-morhtml="true">Monitoraggio della stringa di connessione all'account di archiviazione</li>
</bl></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Elementi aggiuntivi</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Database di rilevamento</td> 
<td data-morhtml="true">Quando viene eseguito il provisioning del servizio BizTalk, vengono immessi i dettagli relativi al database di rilevamento, tra cui il Server database SQL di Azure e il nome del database di rilevamento. Non viene eseguito il backup automatico del database di rilevamento.<br data-morhtml="true" /><br data-morhtml="true" /> <strong data-morhtml="true">Importante</strong><br data-morhtml="true" /> Se il database di rilevamento viene eliminato per errore ed &egrave; necessario ripristinarlo, &egrave; obbligatorio disporre di un backup precedente. Se non &egrave; disponibile un backup, non sar&agrave; possibile recuperare il database di rilevamento e i relativi dati. In questo caso, creare un nuovo database di rilevamento con lo stesso nome. &Egrave; consigliata la replica geografica.</td>
</tr> 
</table>

Creazione di un backup
----------------------

È possibile creare in qualsiasi momento un backup controllato interamente dall'utente. Questa operazione può essere eseguita dal portale di gestione di Azure oppure dalle API REST del servizio BizTalk. Per creare un backup tramite le API REST dei Servizi BizTalk vedere l'articolo relativo al [backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)

In questa sezione sono disponibili le istruzioni su come creare backup tramite il portale di gestione, che consente di creare backup ad hoc o pianificati agli intervalli desiderati.

Prima di creare un backup
-------------------------

Prima di creare un backup assicurarsi di rispettare le considerazioni seguenti:

1.  Prima di eseguire un backup ad hoc per i messaggi attivi in un batch, elaborare il batch di messaggi. In tal modo si impedirà la perdita dei messaggi *se* questo backup viene ripristinato. I messaggi in batch non vengono mai archiviati durante un backup. Con i backup pianificati potrebbe non essere possibile verificare che non vi siano messaggi nel batch all'inizio del backup.

    **Nota**

    Se si crea un backup con la presenza di messaggi attivi in un batch, non verrà eseguito il backup di questi messaggi, che andranno pertanto perduti.

2.  Facoltativo: Nel portale dei Servizi BizTalk arrestare qualsiasi operazione di gestione.
3.  Creare il backup sull'account di archiviazione utilizzando i comandi elencati nell'articolo sul [backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584), disponibili con l'API REST.

[Backup ad hoc](#backupnow)

[Pianificare un backup](#backupschedule)

### Backup ad hoc

1.  Dal portale di gestione di Azure fare clic su BizTalk Services e quindi fare clic sul nome del servizio di cui si desidera eseguire il backup.
2.  Dalla scheda **Dashboard** del servizio BizTalk, fare clic su **Back up** in basso nella pagina.
3.  Nella finestra di dialogo **Back up BizTalk Service** indicare un nome di backup.
4.  Selezionare un account di archiviazione BLOB e fare clic sul segno di spunta per avviare il backup.

Al termine del backup verrà creato un contenitore con il nome di backup specificato nell'account di archiviazione. Questo contenitore include la configurazione del backup del proprio servizio BizTalk.

### Pianificare un backup

1.  Dal portale di gestione di Azure fare clic su BizTalk Services e quindi fare clic sul nome del servizio per cui si desidera pianificare backup automatizzati, quindi fare clic sulla scheda **Configure**.
2.  Nella sezione **Backup Status** selezionare **None** se non si desidera pianificare nessun backup pianificato. Per pianificare backup automatizzati fare clic su **Automatic**.
3.  Nella sezione **Storage Account** selezionare un account di archiviazione di Azure in cui verranno creati i backup.
4.  Nella sezione **Frequency** specificare la data e l'ora di inizio del primo backup, quindi l'intervallo (espresso in giorni) durante il quale verrà creato un backup.
5.  Nella sezione **Retention Days** specificare un intervallo di tempo (in giorni) per cui saranno conservati i backup. Il periodo di conservazione deve essere superiore alla frequenza di backup.
6.  Selezionare la casella di controllo **Always keep at least one backup** per assicurarsi che vi sia almeno un backup disponibile, anche se è scaduto il periodo di conservazione.
7.  Fare clic su **Save**.

Quando si esegue un lavoro di backup pianificato, viene creato un contenitore (in cui archiviare i dati di backup) nell'account di archiviazione specificato dall'utente. Il nome del contenitore è indicato nel formato *Servizio BizTalk nome-data-ora*.

Se un backup non riesce, il relativo stato viene indicato nella pagina Dashboard del servizio BizTalk come **Failed**.

![Last scheduled backup status](./media/biztalk-backup-restore/status-last-backup.png)

Fare clic sul collegamento per passare alla pagina Management Services Operation Logs e ottenere ulteriori informazioni sull'errore. Per ulteriori informazioni sui log operativi riguardo i Servizi BizTalk, vedere l'articolo relativo alla[risoluzione dei problemi quando si utilizzano log operativi nei Servizi BizTalk](http://go.microsoft.com/fwlink/?LinkId=391211).

Ripristino
----------

È possibile ripristinare i backup dal portale di gestione di Azure oppure dalle API REST del servizio BizTalk. In questa sezione sono disponibili le istruzioni su come eseguire il ripristino tramite il portale di gestione. Per eseguire il ripristino mediante l'API REST, vedere l'articolo sul [ripristino del servizio BizTalk da un backup](http://go.microsoft.com/fwlink/p/?LinkID=325582).

### Prima di ripristinare un backup

Quando si esegue il ripristino di un backup, tenere conto di quanto riportato di seguito:

-   Durante il ripristino di un servizio BizTalk è possibile specificare nuovi archivi di rilevamento, archiviazione e monitoraggio.

-   Per ripristinare il Servizio BizTalk utilizzando lo stesso nome, eliminare il Servizio BizTalk esistente prima di avviare l'operazione, altrimenti il ripristino avrà esito negativo.

-   Verranno ripristinati gli stessi dati di runtime EDI. Il backup del runtime EDI consente di archiviare i numeri di controllo. I numeri di controllo ripristinati sono indicati in sequenza a partire dal momento del backup. Se dopo l'ultimo backup si elaborano dei messaggi, il ripristino del contenuto di questo backup può causare la generazione di numeri di controllo duplicati.

#### Per ripristinare un backup

1.  Dal portale di gestione di Azure fare clic su **New**, puntare a **App Services**, **BizTalk Service** e quindi fare clic su **Restore**.

    ![Ripristino di un backup](./media/biztalk-backup-restore/restore-ui.png)

2.  Nella procedura guidata New BizTalk Service - Restore della pagina **Restore BizTalk Service** fare clic sull'icona cartella nella casella di testo **Backup URL** per aprire la finestra di dialogo **Browse Cloud Storage**. Nella finestra di dialogo saranno elencati gli account di archiviazione di Azure.

    Espandere l'account di archiviazione specificato durante la creazione o la pianificazione del backup e quindi selezionare il nome del contenitore dal quale è necessario ripristinare la configurazione del servizio BizTalk.

    Dal riquadro a destra selezionare il file TXT corrispondente al backup dal quale si esegue il ripristino, quindi fare clic su **Open**.

3.  Nella pagina **Restore BizTalk Service** specificare le informazioni seguenti:
    -   Indicare il nome di un servizio BizTalk. Per impostazione predefinita verrà utilizzato il nome del servizio BizTalk di cui è stato eseguito il backup.
    -   Verificare l'URL del dominio, l'edizione e l'area del servizio BizTalk ripristinato.
    -   Scegliere se creare una nuova istanza di database SQL per il database di rilevamento e quindi fare clic sulla freccia destra.

4.  Nella pagina **Specify database settings** verificare il nome del database SQL, specificare il server fisico sul quale verrà creato il database SQL e un nome utente/una password relativi a tale server.

    Se si desidera configurare le impostazioni avanzate del database SQL, selezionare la casella di controllo **Configure Advanced Database Settings** e quindi fare clic sulla freccia destra.

    Se non si desidera configurare le impostazioni avanzate del database, fare clic sulla freccia destra e passare al punto 6 qui di seguito.

5.  Nella pagina **Advanced database settings** selezionare l'edizione del database che si desidera utilizzare (**Web** o **Business**), quindi specificare le dimensioni massime del database e le regole di confronto. Fare clic sulla freccia destra.

6.  Nella pagina **Specify monitoring/archiving settings** creare un nuovo account di archiviazione oppure specificare un account di archiviazione esistente in cui verranno archiviate le informazioni di monitoraggio del servizio BizTalk.

7.  Fare clic sul segno di spunta per avviare il processo di ripristino&lt;/b\>.

8.  Quando il ripristino sarà stato completato correttamente verrà elencato un nuovo servizio BizTalk con stato sospeso nella pagina dei Servizi BizTalk del portale di gestione di Azure.

### Dopo aver ripristinato un backup

Lo stato di un servizio BizTalk ripristinato è sempre **Suspended**. Ciò consente di apportare qualsiasi modifica alla configurazione delle applicazioni BizTalk prima che il nuovo ambiente diventi funzionale. Di seguito sono riportate alcune considerazioni da tenere presenti prima di avviare il servizio BizTalk appena ripristinato:

-   Se le applicazioni del servizio BizTalk Service sono state create mediante l'SDK di Servizi BizTalk di Azure può essere necessario aggiornare le credenziali ACS in tali applicazioni, in modo che funzionino nell'ambiente ripristinato.

-   Può essere opportuno ripristinare un servizio BizTalk in modo da replicare un ambiente di un servizio BizTalk già funzionale. In uno scenario simile, se nel portale Servizi BizTalk originale sono stati configurati dei contratti che utilizzano le condivisioni FTP come fonti, può essere opportuno aggiornare i contratti nell'ambiente appena ripristinato in modo che utilizzino altre fonti o condivisioni FTP. Se questa operazione non viene eseguita è possibile che due diversi contratti tentino di estrarre lo stesso messaggio.

-   Se l'operazione di ripristino è stata utilizzata per ottenere più ambienti di servizi BizTalk, assicurarsi di individuare l'ambiente corretto mediante le applicazioni di Visual Studio, i cmdlet di PowerShell, le API REST oppure le API del modello a oggetti per la gestione dei partner commerciali.

-   È inoltre buona norma configurare i backup automatizzati nell'ambiente del servizio BizTalk appena ripristinato.

Dopo aver soddisfatto queste e altre considerazioni simili, per avviare il servizio aprire la pagina del servizio BizTalk sul portale di gestione di Azure, selezionare il servizio BizTalk appena ripristinato con stato sospeso e quindi fare clic su **Resume** in basso nella pagina.

Passaggi successivi
-------------------

Per eseguire il provisioning di Servizi BizTalk di Azure nel portale di gestione di Azure, vedere l'articolo [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

Vedere anche
------------

-   [Backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Ripristino del servizio BizTalk da un backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: Grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servizi BizTalk: Schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servizi BizTalk: Limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servizi BizTalk: Nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

