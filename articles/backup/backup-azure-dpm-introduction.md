<properties
	pageTitle="Introduzione al backup di DPM di Azure | Microsoft Azure"
	description="Introduzione al backup dei server DPM di Azure usando il servizio Backup di Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Introduzione al backup di Azure DPM

Questo articolo offre un'introduzione all'uso del servizio Backup di Microsoft Azure per proteggere i server e i carichi di lavoro DPM di System Center. Le informazioni dell'articolo riguardano:

- Il funzionamento del backup del server DPM di Azure
- I prerequisiti per eseguire un backup senza problemi
- Gli errori tipici rilevati e come gestirli
- Scenari supportati

DPM di System Center esegue il backup di file e dati delle applicazioni. I dati sottoposti a backup su DPM possono essere archiviati su nastro, disco oppure sottoposti a backup in Azure usando il servizio Backup di Microsoft Azure. DPM interagisce con Backup di Azure nei modi seguenti:

- **DPM distribuito come server fisico o come macchina virtuale locale** : se DPM viene distribuito come server fisico o come una macchina virtuale di Hyper-V locale, è possibile eseguire il backup dei dati su un insieme di credenziali di Backup di Azure, oltre che su dischi e nastri di backup.
- **DPM distribuito come macchina virtuale di Azure**: a partire dalla versione di System Center 2012 R2 con aggiornamento 3, DPM può essere distribuito come macchina virtuale di Azure. Se DPM viene distribuito come macchina virtuale di Azure, sarà possibile eseguire il backup dei dati nei dischi di Azure associati alla macchina virtuale DPM di Azure oppure eseguire l'offload dell'archiviazione dei dati tramite il backup in un insieme di credenziali di Backup di Azure.

## Motivi per eseguire il backup dei server DPM

Di seguito sono elencati i vantaggi aziendali derivanti dall'uso del servizio Backup di Azure per eseguire il backup dei server DPM:

- Per la distribuzione DPM locale è possibile usare il backup di Azure come alternativa alla distribuzione a lungo termine su nastro.
- Per le distribuzioni DPM in Azure, Backup di Azure permette di ridurre lo spazio occupato dai dischi archiviati sul disco di Azure e di aumentare la scalabilità archiviando i dati meno recenti in Backup di Azure e quelli nuovi su disco.

## Funzionamento del backup del server DPM
Per eseguire il backup di una macchina virtuale è prima necessario acquisire uno snapshot dei dati in un momento specifico. Il servizio Backup di Azure avvia il processo di backup all'ora pianificata e avvia l'estensione per il backup per acquisire uno snapshot. L'estensione per il backup si coordina con il servizio VSS in-guest per assicurare coerenza e, dopo averla ottenuta, richiama l'API snapshot del BLOB del servizio Archiviazione di Azure. Questa operazione viene eseguita per ottenere uno snapshot coerente dei dischi della macchina virtuale senza che sia necessario arrestarla.

Dopo l'acquisizione dello snapshot, il servizio Backup di Azure trasferisce i dati all'insieme di credenziali per il backup. Il servizio provvede a identificare e trasferire soltanto i blocchi che sono stati modificati dopo l'ultimo backup, rendendo in questo modo efficienti l'archiviazione dei backup e la rete. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino. È possibile visualizzare tale punto di ripristino nel portale di gestione di Azure.

>[AZURE.NOTE]Per quanto riguarda le macchine virtuali Linux, è possibile eseguire soltanto backup coerenti a livello di file.

## Prerequisiti
Di seguito viene descritto come preparare il servizio Backup di Azure all'esecuzione del backup dei dati DPM:

1. **Creare un insieme di credenziali per il backup**: creare un insieme di credenziali nella console Backup di Azure.
2. **Scaricare le credenziali di insieme**: in Backup di Azure caricare il certificato di gestione creato nell'insieme di credenziali.
3. **Installare Azure Backup Agent e registrare il server**: da Backup di Azure, installare l'agente su ogni server DPM e registrare tale server nell'insieme di credenziali di backup.

### Creare un insieme di credenziali per il backup
Per avviare il backup delle macchine virtuali di Azure è prima necessario creare un insieme di credenziali per il backup. L'insieme di credenziali per il backup è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali contiene inoltre i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

1. Accedere al [portale di gestione](http://manage.windowsazure.com/).
2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Se all'account aziendale sono associate più sottoscrizioni, scegliere quella corretta da associare all'insieme di credenziali per il backup. In ogni sottoscrizione di Azure possono essere presenti più insiemi di credenziali per il backup per organizzare la protezione dei dati.
3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. È necessario che il nome sia univoco per ogni sottoscrizione.
4. In **Area** selezionare l'area geografica per l'insieme di credenziali. Si tenga presente che l'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si desidera proteggere. Se si dispone di macchine virtuali situate in aree geografiche differenti, creare un insieme di credenziali in ciascuna di esse. Per archiviare i dati di backup non è necessario specificare account di archiviazione perché questa operazione verrà gestita in automatico mediante l'insieme di credenziali per il backup e il servizio Backup di Azure.
    > [AZURE.NOTE][aree supportate](http://azure.microsoft.com/regions/#services)

5. In **Subscription** immettere la sottoscrizione di Azure da usare con l'insieme di credenziali per il backup.
6. Fare clic su **Crea insieme di credenziali**. ![Creare un insieme di credenziali per il backup](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale. ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-dpm-introduction/creating-vault.png)

    Viene visualizzato un messaggio per confermare che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali verrà quindi elencato come **Attivo** nella pagina Servizi di ripristino.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. Fare clic sull'insieme di credenziali di backup per accedere alla pagina **Guida introduttiva**, nella quale sono riportate le istruzioni per il backup dei server DPM. ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]Subito dopo la creazione dell'insieme di credenziali, assicurarsi di aver scelto l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options).

### Scaricare le credenziali dell’insieme di credenziali
1. Fare clic su **Servizi di ripristino** e selezionare l'insieme di credenziali di backup. Nella pagina **Guida introduttiva**, fare clic su **Scarica le credenziali dell’insieme di credenziali** per scaricare il file delle credenziali e salvarlo in un percorso sicuro. Dal momento che non è possibile modificare le credenziali, non è necessario aprire tale percorso. Per motivi di sicurezza, la chiave del file scade dopo 48 ore.

2. Copiare il file in un percorso sicuro, facilmente accessibile da parte del server DPM che si desidera registrare nell'insieme di credenziali del servizio Backup di Azure. È necessario selezionare il file quando si installa Azure Backup Agent.

### Installare Azure Backup Agent e registrare il server
Sarà necessario scaricare il file di installazione dell'agente ed eseguirlo su tutti i server DPM che contengono i dati da sottoporre a backup. Gli agenti vengono memorizzati nell'**area download di Azure** e sono caratterizzati da procedure di installazione autonome. Una volta avviata la procedura, l'agente viene installato e il server DPM viene registrato con l'insieme di credenziali. Si noti che:

- Nel server DPM, è necessario disporre di autorizzazioni amministrative per installare l'agente.
- Per installare l'agente su più server DPM, è possibile inserire il file del programma di installazione in una risorsa di rete condivisa oppure usare criteri di gruppo o prodotti di gestione come System Center Configuration Manager.
- Al termine della procedura di installazione, non è necessario riavviare il server DPM.

#### Installare l'agente di backup e registrare il server

1. Nella pagina **Guida introduttiva** dell'insieme di credenziali del servizio Backup di Azure, accedere a **Scarica Azure Backup Agent** e selezionare **Per Windows Server, System Center Data Protection Manager o client Windows**. Scaricare l'applicazione nel server DPM in cui si desidera eseguirla.
2. Eseguire il file di installazione **MARSAgentInstaller.exe**. Accettare le condizioni di servizio e scegliere di installare eventuali prerequisiti software mancanti.
3. Nella pagina **Impostazioni di installazione**, selezionare la **cartella di installazione** e il **percorso cache**.

    Il percorso della cartella cache predefinito è <system drive>:\Programmi\Azure Backup Agent. Nel percorso della cache, tramite la procedura di installazione viene creata una cartella denominata **Scratch** all'interno della cartella **Azure Backup Agent**. Il percorso della cache deve disporre di almeno 2,5 GB di spazio libero oppure di una quantità pari al 10% della dimensione dei dati che verranno sottoposti a backup in Azure. Solo gli amministratori del sistema locale e i membri del gruppo Administrators possono accedere alla directory della cache per impedire attacchi di tipo Denial of Service.

4. Nella pagina **Configurazione proxy**, regolare le impostazioni personalizzate del proxy in modo che l'agente possa connettersi ad Azure. Se non si configurano le impostazioni, nel server DPM verranno usate quelle predefinite per l'accesso a Internet. Si noti che se si usa un server proxy che necessita di autenticazione, è necessario inserire i dettagli in questa pagina.
5. È opportuno attivare gli aggiornamenti nella pagina **Acconsentire esplicitamente gli aggiornamenti Microsoft**. Se gli aggiornamenti automatici sono stati già attivati per il server, è possibile ignorare questo passaggio. Si noti che le impostazioni di Microsoft Update sono valide per tutti gli aggiornamenti dei prodotti Microsoft e non si riferiscono esclusivamente ad Azure Backup Agent.
6. Viene visualizzata la pagina **Installazione**. La procedura di installazione consente di verificare che venga installato il software richiesto e successivamente completa l'installazione. Al termine dell'operazione, viene visualizzato un messaggio di conferma in merito all'installazione di Azure Backup Agent. A questo punto, è possibile verificare la disponibilità di aggiornamenti. È consigliabile abilitare la verifica relativa alla disponibilità degli aggiornamenti.
7. Per registrare il server nell'insieme di credenziali, fare clic su **Procedi con la registrazione**.
8. Nella pagina **Identificazione insieme di credenziali** selezionare il file di registrazione creato nell'insieme di credenziali del servizio Backup di Azure.
9. Nella pagina **Impostazione crittografia**, specificare i dettagli della passphrase oppure crearla automaticamente.
10. Fare clic su Crea passphrase e in seguito Copia negli appunti. Verrà visualizzato un messaggio di conferma in merito alla copia della passphrase negli appunti. A questo punto, si consiglia di aprire il Blocco note, incollare la passphrase e salvare il file. Inoltre, è opportuno stampare il file e conservarlo in modo sicuro. Fare clic su Registra per registrare il server DPM nell'insieme di credenziali di Backup.

    > [AZURE.TIP]
11. Fare clic su **Register**.

    Al termine della registrazione, nella console DPM viene visualizzata la disponibilità del servizio Backup di Azure.

    Backup di Azure eseguirà sempre la crittografia dei dati all'origine, usando la passphrase (stringa di caratteri alfanumerici) specificata o creata automaticamente.
    >[AZURE.NOTE]Backup di Azure non conserva mai la passphrase, pertanto, se viene persa, i dati non possono essere ripristinati oppure recuperati. Si consiglia di salvare la chiave in un percorso esterno.

Quando si specifica una passphrase e si fa clic su **Fine**, è necessario attendere alcuni secondi per consentire all'agente di registrare il server di produzione nell'insieme di credenziali di backup. Al termine della registrazione nell'insieme di credenziali, viene visualizzata la pagina riepilogativa denominata **Registrazione server**.

## Requisiti e limitazioni

- DPM può essere eseguito come server fisico o come macchina virtuale Hyper-V installata in System Center 2012 SP1 o System Center 2012 R2. Inoltre, è possibile eseguirlo come macchina virtuale di Azure in System Center 2012 R2 (con almeno l'aggiornamento cumulativo 3 di DPM 2012 R2) oppure come macchina virtuale di Windows in VMware con System Center 2012 R2 e almeno il relativo aggiornamento cumulativo 5.
- Se DPM viene eseguito con System Center 2012 SP1, è necessario installare l'aggiornamento cumulativo 2 per System Center Data Protection Manager SP1. Tale procedura è necessaria prima di poter installare Azure Backup Agent.
- È necessario che nel server DPM siano installati Windows PowerShell e .Net Framework 4.5.
- DPM può eseguire il backup della maggior parte dei carichi di lavoro nel servizio Backup di Azure. Per visualizzare un elenco completo degli elementi supportati, consultare gli elementi supportati di Backup di Azure riportati di seguito.
- Usando l'opzione "Copia su nastro", non è possibile ripristinare i dati memorizzati in Backup di Azure.
- È necessario disporre di un account di Azure su cui è abilitata la funzionalità Backup di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
- Per usare Backup di Azure è necessario che Azure Backup Agent sia installato sui server da sottoporre a backup. Ogni server deve disporre di almeno 2,5 GB di spazio di archiviazione locale dedicato al percorso della cache. Tuttavia, per il percorso della cache si consiglia di disporre di almeno 15 GB di spazio di archiviazione locale.
- I dati verranno memorizzati nell'archiviazione relativa all'insieme di credenziali di Azure. Non esistono limiti relativi alla quantità di dati che è possibile sottoporre a backup in un insieme di credenziali di Backup di Azure, tuttavia la dimensione dell'origine dati (ad esempio, un database o una macchina virtuale) non deve superare i 1,65 TB.

Il backup in Azure è supportato per i tipi di file seguenti:

- Crittografati (solo backup completi)
- Compressi (backup incrementali supportati)
- Sparse (backup incrementali supportati)
- Compressi e sparse (considerati come sparse)

Questi tipi di file non sono supportati:

- I server nei file system che distinguono fra minuscole e maiuscole non sono supportati.
- Collegamenti reali (ignorati)
- Reparse point (ignorati)
- Crittografati e compressi (ignorati)
- Crittografati e sparse (ignorati)
- Flusso compresso
- Flusso di tipo sparse

>[AZURE.NOTE]A partire dalla versione di System Center 2012 DPM con SP1, è possibile eseguire il backup su carichi di lavoro (protetti da DPM) in Azure usando il servizio Backup di Microsoft Azure.

<!---HONumber=62-->