<properties
  pageTitle="Preparazione dell'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure | Microsoft Azure"
  description="Assicurarsi che l'ambiente sia preparato correttamente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure."
  services="backup"
  documentationCenter=""
  authors="trinadhk"
  manager="shreeshd"
  editor=""
  keywords="server di backup di azure; insieme di credenziali di backup"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/10/2016"
  ms.author="jimpark;trinadhk;pullabhk"/>

# Preparazione del backup dei carichi di lavoro con il server di Backup di Azure

> [AZURE.SELECTOR]
- [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Server di Backup di Azure (classico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (classico)](backup-azure-dpm-introduction-classic.md)

Questo articolo illustra la preparazione dell'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure.

> [AZURE.NOTE] Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../resource-manager-deployment-model.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.

Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console.

>[AZURE.WARNING] Il server di Backup di Azure eredita le funzionalità di Data Protection Manager (DPM) per il backup del carico di lavoro. Per alcune di queste funzionalità saranno presenti puntatori alla documentazione di DPM. Il server di Backup di Azure, tuttavia, non fornisce la protezione su nastro o l'integrazione con System Center.

## 1\. Computer Windows Server

Per rendere operativo il server di Backup di Azure, è prima di tutto necessario avere un computer Windows Server.

| Percorso | Requisiti minimi | Istruzioni aggiuntive |
| -------- | -------------------- | ----------------------- |
| Azure | Macchina virtuale IaaS di Azure<br><br>A2 Standard: 2 core, 3,5 GB di RAM | È possibile iniziare con una semplice immagine della raccolta di Windows Server 2012 R2 Datacenter. La [protezione dei carichi di lavoro IaaS con il server di Backup di Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) è piuttosto complessa. Assicurarsi di leggere completamente l'articolo prima di distribuire la macchina. |
| Locale | Macchina virtuale Hyper-V,<br> Macchina virtuale VMWare<br> oppure host fisico<br><br>2 core e 4 GB di RAM | È possibile deduplicare la risorsa di archiviazione DPM usando la deduplicazione di Windows Server. Vedere altre informazioni sull'interazione di [DPM e deduplicazione](https://technet.microsoft.com/library/dn891438.aspx) in caso di distribuzione in macchine virtuali Hyper-V. |

> [AZURE.NOTE] È consigliabile installare il server di Backup di Azure in un computer con Windows Server 2012 R2 Datacenter. Molti prerequisiti sono soddisfatti automaticamente con la versione più recente del sistema operativo Windows.

Se si prevede di aggiungere il server a un dominio in futuro, è consigliabile eseguire l'attività di aggiunta al dominio prima dell'installazione del server di Backup di Azure. *Non è supportato* lo spostamento di un server di Backup di Azure esistente in un nuovo dominio dopo la distribuzione.

## 2\. Insieme di credenziali dei servizi di ripristino

Indipendentemente dal fatto che i dati di backup vengano inviati ad Azure o vengano archiviati in locale, il software deve essere connesso ad Azure. In particolare, il computer del server di Backup di Azure deve essere registrato in un insieme di credenziali dei servizi di ripristino.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Sfoglia** dal menu Hub e digitare **Servizi di ripristino** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insieme di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.

3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    Si apre il pannello degli insiemi di credenziali dei servizi di ripristino in cui viene chiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)

4. Nel campo **Nome** digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Fare clic su **Sottoscrizione** per visualizzare l'elenco di sottoscrizioni disponibili. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Fare clic su **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure fare clic su **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Uso del portale di Azure per distribuire e gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali.

8. Fare clic su **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. Una volta creato, l'insieme di credenziali viene aperto nel portale.

### Impostare la replica di archiviazione

L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e con [ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere la [panoramica Replica di archiviazione di Azure](../storage/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Selezionare l'insieme di credenziali per aprire il dashboard dell'insieme di credenziali e il pannello Impostazioni. Se il pannello **Impostazioni** non si apre, fare clic su **Tutte le impostazioni** nel dashboard dell'insieme di credenziali.

2. Nel pannello **Impostazioni** fare clic su **Infrastruttura di backup** > **Configurazione backup** per aprire il pannello **Configurazione backup**. Nel pannello **Configurazione backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la macchina virtuale all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## 3\. Pacchetto software

### Download del pacchetto software
1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Se è già aperto un insieme di credenziali dei servizi di ripristino, procedere al passaggio 3. Se non è stato aperto nessun insieme di credenziali dei servizi di ripristino, ma si è nel portale di Azure, scegliere **Sfoglia** dal menu Hub.

    - Nell'elenco di risorse digitare **Servizi di ripristino**.
    - Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
    
    - Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

    Viene aperto il dashboard dell'insieme di credenziali selezionato.

    ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

3. Per impostazione predefinita si apre il pannello Impostazioni. Se non si apre, fare clic su **Impostazioni** per aprire il pannello delle impostazioni.

    ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-setting.png)

4. Fare clic su **Backup** in **Attività iniziali** per aprire Attività iniziali guidate.

    ![Attività iniziali di backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

5. Nella finestra Attività iniziali viene automaticamente selezionata la schermata Obiettivi di Backup. ![Backup-obiettivi-predefinito-aperto](./media/backup-azure-microsoft-azure-backup/getting-started.png)

    Nella sezione **Obiettivi di Backup** selezionare *locale* per *la posizione in cui viene eseguito il carico di lavoro*.

    ![locale e carichi di lavoro come obiettivi](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

6. Selezionare i carichi di lavoro che si vuole proteggere usando il server di Backup di Azure nei *carichi di lavoro da proteggere* e fare clic su **OK**.

    > [AZURE.NOTE] Se si prevede di proteggere solo file e cartelle, è consigliabile usare un agente di Backup di Azure. Se si prevede di proteggere più carichi di lavoro oltre a file e cartelle oppure se si vuole in futuro espandere i requisiti di protezione, selezionare tutti i carichi di lavoro.

    Questa scelta modifica Attività iniziali guidate per preparare l'infrastruttura di protezione dei carichi di lavoro da locale in Azure.

    ![Attività iniziali guidate modifica](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)
  
7. Nel pannello **Prepara infrastruttura**, fare clic su **Scarica Server di Backup di Azure** e scarica Credenziali di insieme delle credenziali usate durante la registrazione del server di Backup di Azure per l'insieme di credenziali dei servizi di ripristino. Verrà visualizzata la pagina dell'Area download da cui è possibile scaricare il pacchetto software.

    ![Preparare l'infrastruttura per il Server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

8. Selezionare tutti i file e fare clic su **Avanti**. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure e salvarli nella stessa cartella.

    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Poiché le dimensioni di download di tutti questi file è pari a > 3 GB, per il download completo potrebbero essere necessari fino a 60 minuti su un collegamento di download a 10 Mbps.


### Estrazione del pacchetto software

Dopo aver scaricato tutti i file, fare clic su **MicrosoftAzureBackupInstaller.exe**. Verrà avviata l'**Installazione guidata di Backup di Microsoft Azure** per estrarre i file di installazione in un percorso specificato dall'utente. Continuare la procedura guidata e fare clic sul pulsante **Estrai** per avviare il processo di estrazione.

> [AZURE.WARNING] Per estrarre i file di installazione sono necessari almeno 4 GB di spazio libero.


![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Al termine del processo di estrazione, selezionare la casella per avviare il file *setup.exe* appena estratto e iniziare l'installazione del server di Backup di Microsoft Azure, quindi fare clic sul pulsante **Fine**.

### Installazione del pacchetto software

1. Fare clic su **Backup di Microsoft Azure** per avviare l'installazione guidata.

    ![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. Nella schermata iniziale fare clic sul pulsante **Avanti**. Verrà visualizzata la sezione *Controlli dei prerequisiti*. In questa schermata fare clic sul pulsante **Controlla** per determinare se i prerequisiti hardware e software per il server di Backup di Azure sono stati soddisfatti. Se tutti i prerequisiti sono stati soddisfatti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Fare clic sul pulsante **Avanti**.

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. Il server di Backup di Microsoft Azure richiede SQL Server Standard e il pacchetto di installazione del server di Backup di Azure include i file binari appropriati di SQL Server necessari. Quando si avvia una nuova installazione del server di Backup di Azure, è consigliabile selezionare l'opzione **Installa una nuova istanza di SQL Server con questa installazione** e fare clic sul pulsante **Controlla e installa**. Una volta che i prerequisiti vengono installati correttamente, fare clic su **Avanti**.

    ![Server di backup di Azure - Controllo SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se si verifica un errore con l'indicazione di riavviare il computer, eseguire questa operazione e fare clic su **Controlla di nuovo**.

    > [AZURE.NOTE] Il server di Backup di Azure non funzionerà con un'istanza remota di SQL Server. L'istanza usata dal server di Backup di Azure deve essere locale.

4. Specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

5. Fornire una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Selezionare se si vuole usare *Microsoft Update* per cercare gli aggiornamenti e fare clic su **Avanti**.

    >[AZURE.NOTE] È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. L'installazione avviene a fasi. Nella prima fase viene eseguita l'installazione dell'Agente servizi di ripristino di Microsoft Azure sul server. La procedura guidata verifica anche la connettività Internet. Se la connettività Internet è disponibile, si può procedere con l'installazione, in caso contrario è necessario fornire i dettagli relativi al proxy per connettersi a Internet.

    Il passaggio successivo consiste nel configurare l'Agente servizi di ripristino di Microsoft Azure. Come parte della configurazione, è necessario specificare le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali dei servizi di ripristino. Sarà anche necessario specificare una passphrase per crittografare/decrittografare i dati inviati tra Azure e l'istanza locale. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Continuare la procedura guidata per completare la configurazione dell'agente.

    ![PreReq2 del server di backup di Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Al termine della registrazione del server di Backup di Microsoft Azure, l'installazione guidata generale passa all'installazione e alla configurazione dei componenti di SQL Server e del server di Backup di Azure. Al termine dell'installazione dei componenti di SQL Server, vengono installati i componenti del server di Backup di Azure.

    ![Server di backup di Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Al termine della fase di installazione, verranno create anche le icone sul desktop per il prodotto. È sufficiente fare doppio clic sull'icona per avviare il prodotto.

### Aggiungere risorse di archiviazione di backup

La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura attuale del server di Backup di Azure l'insieme di credenziali di Backup di Azure contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima e obbligatoria copia di backup.

## 4\. Connettività di rete

Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per verificare se il computer è connesso ad Azure, usare ```Get-DPMCloudConnection``` cmdlet nella console di PowerShell del server di Backup di Azure. C'è connettività solo se l'output di cmdlet è TRUE.

Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro. Per verificare lo stato della sottoscrizione e gestirla, accedere al [portale di sottoscrizione](https://account.windowsazure.com/Subscriptions).

Dopo avere verificato lo stato della connettività di Azure e della sottoscrizione di Azure, è possibile usare la tabella seguente per scoprire l'impatto della funzionalità di backup/ripristino offerta.

| Stato di connettività | Sottoscrizione di Azure | Backup in Azure| Backup su disco | Ripristino da Azure | Ripristino da disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Connesso | Attivo | Consentito | Consentito | Consentito | Consentito |
| Connesso | Scaduto | Arrestato | Arrestato | Consentito | Consentito |
| Connesso | Deprovisioning eseguito | Arrestato | Arrestato | Arrestato e punti di ripristino di Azure eliminati | Arrestato |
| Connettività persa > 15 giorni | Attivo | Arrestato | Arrestato | Consentito | Consentito |
| Connettività persa > 15 giorni | Scaduto | Arrestato | Arrestato | Consentito | Consentito |
| Connettività persa > 15 giorni | Deprovisioning eseguito | Arrestato | Arrestato | Arrestato e punti di ripristino di Azure eliminati | Arrestato |

### Recupero dalla perdita di connettività
Se è presente un firewall o un proxy che impedisce l'accesso ad Azure, sarà necessario aggiungere all'elenco elementi consentiti gli indirizzi di dominio seguenti nel profilo del firewall/proxy:

- www.msftncsi.com
- *.Microsoft.com
- windowsazure.com
- *.microsoftonline.com
- *.windows.net

Dopo il ripristino della connettività ad Azure nel computer del server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. La tabella precedente include dettagli sulle operazioni consentite quando il computer è "Connesso".

### Gestione degli stati della sottoscrizione

È possibile fare in modo che una sottoscrizione di Azure passi da uno stato *Scaduta* o *Deprovisioning eseguito* allo stato *Attiva*. Questa operazione tuttavia influisce sul comportamento del prodotto quando lo stato della sottoscrizione non è *Attiva*:

- Una sottoscrizione con *Deprovisioning eseguito* perde funzionalità per il periodo in cui è sottoposta a deprovisioning. Dopo l'impostazione su *Attiva*, la funzionalità di backup/ripristino del prodotto viene riattivata. I dati di backup nel disco locale possono essere recuperati anche se sono stati memorizzati con un periodo di conservazione sufficientemente elevato. I dati di backup in Azure vengono persi definitivamente quando la sottoscrizione passa allo stato *Deprovisioning eseguito*.
- Una sottoscrizione *Scaduta* perde funzionalità solo fino a quando non viene di nuovo resa *Attiva*. Eventuali backup pianificati per il periodo in cui la sottoscrizione è *Scaduta* non verranno eseguiti.


## Risoluzione dei problemi

In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, o di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni. È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)


## Passaggi successivi

Per informazioni dettagliate sulla [preparazione dell'ambiente per DPM](https://technet.microsoft.com/library/hh758176.aspx), vedere il sito Web Microsoft TechNet. Contiene anche informazioni sulle configurazioni supportate in cui è possibile distribuire e usare il server di Backup di Azure.

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

- [Backup di SQL Server](backup-azure-backup-sql.md)
- [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
- [Backup del server alternativo](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0518_2016-->