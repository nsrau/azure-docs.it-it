---
title: Preparazione dell'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure | Microsoft Docs
description: Assicurarsi che l'ambiente sia preparato correttamente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure.
services: backup
documentationcenter: ''
author: pvrk
manager: shivamg
editor: ''
keywords: server di backup di azure; insieme di credenziali di backup

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: jimpark;trinadhk;pullabhk; markgal

---
# Preparazione del backup dei carichi di lavoro con il server di Backup di Azure
> [!div class="op_single_selector"]
> * [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Server di Backup di Azure (classico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (classico)](backup-azure-dpm-introduction-classic.md)
> 
> 

Questo articolo illustra la preparazione dell'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure. Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console.

> [!WARNING]
> Il server di Backup di Azure eredita le funzionalità di Data Protection Manager (DPM) per il backup del carico di lavoro. Per alcune di queste funzionalità saranno presenti puntatori alla documentazione di DPM. Il server di Backup di Azure, tuttavia, non fornisce la protezione su nastro o l'integrazione con System Center.
> 
> 

## 1\. Computer Windows Server
![passaggio1](./media/backup-azure-microsoft-azure-backup/step1.png)

Per rendere operativo il server di Backup di Azure, è prima di tutto necessario avere un computer Windows Server.

| Percorso | Requisiti minimi | Istruzioni aggiuntive |
| --- | --- | --- |
| Azure |Macchina virtuale IaaS di Azure<br><br>A2 Standard: 2 core, 3,5 GB di RAM |È possibile iniziare con una semplice immagine della raccolta di Windows Server 2012 R2 Datacenter. La [protezione dei carichi di lavoro IaaS con il server di Backup di Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) è piuttosto complessa. Assicurarsi di leggere completamente l'articolo prima di distribuire la macchina. |
| Locale |Macchina virtuale Hyper-V,<br> Macchina virtuale VMWare<br> oppure host fisico<br><br>2 core e 4 GB di RAM |È possibile deduplicare la risorsa di archiviazione DPM usando la deduplicazione di Windows Server. Vedere altre informazioni sull'interazione di [DPM e deduplicazione](https://technet.microsoft.com/library/dn891438.aspx) in caso di distribuzione in macchine virtuali Hyper-V. |

> [!NOTE]
> È consigliabile installare il server di Backup di Azure in un computer con Windows Server 2012 R2 Datacenter. Molti prerequisiti sono soddisfatti automaticamente con la versione più recente del sistema operativo Windows.
> 
> 

Se si prevede di aggiungere il server a un dominio in futuro, è consigliabile eseguire l'attività di aggiunta al dominio prima dell'installazione del server di Backup di Azure. *Non è supportato* lo spostamento di un server di Backup di Azure esistente in un nuovo dominio dopo la distribuzione.

## 2\. Insieme di credenziali per il backup
![passaggio2](./media/backup-azure-microsoft-azure-backup/step2.png)

Indipendentemente dal fatto che i dati di backup vengano inviati ad Azure o vengano archiviati in locale, il software deve essere connesso ad Azure. In particolare, il computer del server di Backup di Azure deve essere registrato in un insieme di credenziali di backup.

Per creare un insieme di credenziali per il backup:

1. Accedere al [portale di gestione](http://manage.windowsazure.com/).
2. Fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Se all'account aziendale sono associate più sottoscrizioni, scegliere quella corretta da associare all'insieme di credenziali per il backup.
3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. È necessario che il nome sia univoco per ogni sottoscrizione.
4. In **Region** selezionare l'area geografica per l'insieme di credenziali. In genere, l'area dell'insieme di credenziali viene selezionata in base alla sovranità dei dati o ai vincoli relativi alla latenza di rete.
   
    ![Creare un insieme di credenziali per il backup](./media/backup-azure-microsoft-azure-backup/backup_vaultcreate.png)
5. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.
   
    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-microsoft-azure-backup/creating-vault.png)
6. Viene visualizzato un messaggio per confermare che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali verrà quindi elencato come attivo nella pagina Servizi di ripristino. ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-microsoft-azure-backup/backup_vaultslist.png)
   
   > [!IMPORTANT]
   > Subito dopo la creazione dell'insieme di credenziali, assicurarsi di avere scelto l'opzione di ridondanza dell'archiviazione corretta. Per altre informazioni sulle opzioni [ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e [ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage) leggere questa [panoramica](../storage/storage-redundancy.md).
   > 
   > 

## 3\. Pacchetto software
![passaggio3](./media/backup-azure-microsoft-azure-backup/step3.png)

### Download del pacchetto software
In modo analogo alle credenziali dell'insieme di credenziali, è possibile scaricare il server di Backup di Microsoft Azure per i carichi di lavoro dell'applicazione dalla **pagina Avvio rapido** dell'insieme di credenziali per il backup.

1. Fare clic sull'opzione **per carichi di lavoro dell'applicazione (da disco a disco a cloud)**. Verrà visualizzata la pagina dell'Area download da cui è possibile scaricare il pacchetto software.
   
    ![Schermata iniziale di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Fare clic su **Download**.
   
    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Selezionare tutti i file e fare clic su **Avanti**. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure e salvarli nella stessa cartella. ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)
   
    Poiché le dimensioni di download di tutti questi file è pari a > 3 GB, per il download completo potrebbero essere necessari fino a 60 minuti su un collegamento di download a 10 Mbps.

### Estrazione del pacchetto software
Dopo aver scaricato tutti i file, fare clic su **MicrosoftAzureBackupInstaller.exe**. Verrà avviata l'**Installazione guidata di Backup di Microsoft Azure** per estrarre i file di installazione in un percorso specificato dall'utente. Continuare la procedura guidata e fare clic sul pulsante **Estrai** per avviare il processo di estrazione.

> [!WARNING]
> Per estrarre i file di installazione sono necessari almeno 4 GB di spazio libero.
> 
> 

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
   
   > [!NOTE]
   > Il server di Backup di Azure non funzionerà con un'istanza remota di SQL Server. L'istanza usata dal server di Backup di Azure deve essere locale.
   > 
   > 
4. Specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.
   
    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)
   
    La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Fornire una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.
   
    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selezionare se si vuole usare *Microsoft Update* per cercare gli aggiornamenti e fare clic su **Avanti**.
   
   > [!NOTE]
   > È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.
   > 
   > 
   
    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.
   
    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L'installazione avviene a fasi. Nella prima fase viene eseguita l'installazione dell'Agente servizi di ripristino di Microsoft Azure sul server. La procedura guidata verifica anche la connettività Internet. Se la connettività Internet è disponibile, si può procedere con l'installazione, in caso contrario è necessario fornire i dettagli relativi al proxy per connettersi a Internet.
   
    Il passaggio successivo consiste nel configurare l'Agente servizi di ripristino di Microsoft Azure. Come parte della configurazione, sarà necessario specificare le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali per il backup. Sarà anche necessario specificare una passphrase per crittografare/decrittografare i dati inviati tra Azure e l'istanza locale. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Continuare la procedura guidata per completare la configurazione dell'agente.
   
    ![PreReq2 del server di backup di Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Al termine della registrazione del server di Backup di Microsoft Azure, l'installazione guidata generale passa all'installazione e alla configurazione dei componenti di SQL Server e del server di Backup di Azure. Al termine dell'installazione dei componenti di SQL Server, vengono installati i componenti del server di Backup di Azure.
   
    ![Server di backup di Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Al termine della fase di installazione, verranno create anche le icone sul desktop per il prodotto. È sufficiente fare doppio clic sull'icona per avviare il prodotto.

### Aggiungere risorse di archiviazione di backup
La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura attuale del server di Backup di Azure l'insieme di credenziali di Backup di Azure contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima e obbligatoria copia di backup.
> 
> 

## 4\. Connettività di rete
![passaggio4](./media/backup-azure-microsoft-azure-backup/step4.png)

Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per verificare se il computer è connesso ad Azure, usare ```Get-DPMCloudConnection``` commandlet nella console di PowerShell del server di Backup di Azure. Se l'output del cmdlet è TRUE, la connettività è disponibile. In caso contrario, non è disponibile connettività.

Allo stesso tempo è necessario che la sottoscrizione di Azure sia in uno stato integro. Per verificare lo stato della sottoscrizione e gestirla, accedere al [portale delle sottoscrizioni](https://account.windowsazure.com/Subscriptions).

Dopo aver verificato lo stato della connettività di Azure e della sottoscrizione di Azure, è possibile usare la tabella seguente per scoprire l'impatto della funzionalità di backup/ripristino offerta.

| Stato di connettività | Sottoscrizione di Azure | Backup in Azure | Backup su disco | Ripristino da Azure | Ripristino da disco |
| --- | --- | --- | --- | --- | --- |
| Connesso |Attivo |Consentito |Consentito |Consentito |Consentito |
| Connesso |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connesso |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |
| Connettività persa > 15 giorni |Attivo |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |

### Recupero dalla perdita di connettività
Se è presente un firewall o un proxy che impedisce l'accesso ad Azure, sarà necessario aggiungere all'elenco elementi consentiti gli indirizzi di dominio seguenti nel profilo del firewall/proxy:

* www.msftncsi.com
* *.Microsoft.com
* windowsazure.com
* *.microsoftonline.com
* *.windows.net

Dopo il ripristino della connettività ad Azure nel computer del server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. La tabella precedente include dettagli sulle operazioni consentite quando il computer è "Connesso".

### Gestione degli stati della sottoscrizione
È possibile modificare lo stato di una sottoscrizione di Azure da *Scaduta* o *Deprovisioning eseguito* ad *Attiva*. Questa operazione tuttavia influisce sul comportamento del prodotto quando lo stato della sottoscrizione non è *Attiva*:

* Una sottoscrizione con stato *Deprovisioning eseguito* perde funzionalità per il periodo in cui è sottoposta a deprovisioning. Dopo l'impostazione su *Attiva*, la funzionalità di backup/ripristino del prodotto viene riattivata. I dati di backup nel disco locale possono essere recuperati anche se sono stati memorizzati con un periodo di conservazione sufficientemente elevato. I dati di backup in Azure, invece, vengono persi definitivamente al passaggio della sottoscrizione allo stato *Deprovisioning eseguito*.
* Una sottoscrizione *Scaduta* perde funzionalità solo fino a quando non viene resa di nuovo *Attiva*. Eventuali backup pianificati per il periodo in cui la sottoscrizione è *Scaduta* non verranno eseguiti.

## Risoluzione dei problemi
In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, o di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni. È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## Passaggi successivi
Per informazioni dettagliate sulla [preparazione dell'ambiente per DPM](https://technet.microsoft.com/library/hh758176.aspx), vedere il sito Web Microsoft TechNet. Contiene anche informazioni sulle configurazioni supportate in cui è possibile distribuire e usare il server di Backup di Azure.

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

* [Backup di SQL Server](backup-azure-backup-sql.md)
* [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
* [Backup del server alternativo](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0831_2016-->