<properties
	pageTitle="Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici | Microsoft Auzre" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei server locali in Azure o in un data center secondario. Utilizzare questo articolo per monitorare e risolvere i problemi di VMM e di protezione dei siti di Hyper-V" 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="anbacker"/>
	
# Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici

La Guida al monitoraggio e la alla risoluzione dei problemi consente come monitorare e apprendere lo stato della replica di rilevamento e risoluzione dei problemi di tecniche per Azure Site Recovery.

## Informazioni sui componenti

### Distribuzione del sito VMware o fisico per la replica tra locale e Azure.
Per configurare il ripristino di emergenza tra computer fisici/VMware locali; il server di configurazione, il server Master di destinazione e il server di elaborazione devono essere configurati. Durante l'abilitazione della protezione per il server di origine Azure Site Recovery installerà il servizio Mobility. Dopo l’interruzione locale una volta che si è verificato il failover del server di origine in Azure, i clienti devono configurare un Server di elaborazione in Azure e un server Master di destinazione locale per proteggere il server di origine dalla ricostruzione locale.

![Distribuzione del sito VMware o fisico per la replica tra locali e Azure.](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### Distribuzione del sito VMM per la replica tra siti locali.

Nell'ambito dell'impostazione di ripristino di emergenza tra due siti locali; il provider di Azure Site Recovery deve essere scaricato e installato nel server VMM. ll provider richiede la connettività a Internet per garantire che tutte le operazioni attivate dal portale di Azure vengano convertite in operazioni locali, tra cui l'abilitazione della protezione, l'arresto delle macchine virtuali primarie nell'ambito del failover e così via.

![Distribuzione del sito VMM per la replica tra siti locali](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Distribuzione del sito VMM per la replica tra siti locali e Azure.

Nell'ambito dell'impostazione di ripristino di emergenza tra due siti locali e Azure; il provider di Azure Site Recovery deve essere scaricato e installato nel server VMM insieme all'agent di Azure Recovery Services che deve essere installato in ciascun host Hyper-V. Per altre informazioni, fare riferimento all'argomento sulla [protezione dal sito ad Azure](./site-recovery-understanding-site-to-azure-protection.md).

![Distribuzione del sito VMM per la replica tra siti locali e Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Distribuzione del sito Hyper-V per la replica tra siti locali e Azure.

Si tratta della stessa situazione di una distribuzione VMM: l'unica differenza è dovuta al fatto che il provider e l'agente vengono installati nell'host Hyper-V. Per altre informazioni, fare riferimento all'argomento sulla [protezione dal sito ad Azure](./site-recovery-understanding-site-to-azure-protection.md).

## Monitorare la configurazione, la protezione e il ripristino

Ogni operazione in ASR viene controllata e monitorata nella scheda "JOBS". In caso di eventuali errori di configurazione, protezione o ripristino, passare alla scheda JOBS e verificare se vi sono errori.

![Monitorare la configurazione, la protezione e il ripristino](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Dopo aver individuato gli errori nella visualizzazione PROCESSI, selezionare il processo e fare clic su DETTAGLI ERRORE per tale processo.

![Monitorare la configurazione, la protezione e il ripristino](media/site-recovery-monitoring-and-troubleshooting/image4.png)

I dettagli errore consentiranno di identificare eventuali cause e suggerimenti per il problema.

![Monitorare la configurazione, la protezione e il ripristino](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Nel caso sopra sembra esserci un'altra operazione in corso a causa della quale la configurazione di protezione ha esito negativo. Assicurarsi di risolvere il problema in base all'indicazione fornita; successivamente fare clic su RIAVVIA per riavviare l'operazione.

![Monitorare la configurazione, la protezione e il ripristino](media/site-recovery-monitoring-and-troubleshooting/image6.png)

L'opzione per RIAVVIARE non è sempre disponibile per tutte le operazioni; coloro che non dispongono dell'opzione RIAVVIA devono tornare all'oggetto e riavviare l'operazione. Ogni PROCESSO può essere annullato in qualsiasi momento utilizzando il pulsante di annullamento in corso.

![Monitorare la configurazione, la protezione e il ripristino](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Monitorare lo stato della replica per la macchina virtuale

Provider di ripristino automatico di sistema centrale e di monitoraggio tramite il portale di Azure per ogni entità protetta. Passare alla scheda ELEMENTI PROTETTI e selezionare CLOUD VMM o GRUPPI DI PROTEZIONE. Scheda CLOUD VMM è solo per le distribuzioni di tipo VMM e tutti gli altri scenari avere entità protette nella scheda GRUPPI PROTEZIONE dati.

![Monitorare lo stato della replica per la macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Successivamente, selezionare l'entità protetta sotto il rispettivo cloud o il gruppo protezione dati. Dopo aver selezionato l'entità protetta, tutte le operazioni consentite vengono visualizzate nel riquadro inferiore.

![Monitorare lo stato della replica per la macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Come illustrato in precedenza, nel caso in cui lo l'integrità della macchina virtuale che integrità è fondamentale: è possibile fare clic sul pulsante i Dettagli dell'errore nella parte inferiore per visualizzare l'errore. Risolvere il problema in base alle "Cause possibili" e alle "Raccomandazioni" sopra menzionate.

![Monitorare lo stato della replica per la macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Monitorare lo stato della replica per la macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Nota: se vi sono operazioni attive in corso o non riuscite, passare alla visualizzazione PROCESSI come indicato in precedenza per visualizzare l'errore specifico del processo.

## Risoluzione dei problemi di Hyper-V a livello locale

Connettersi alla console di gestione di Hyper-V locale, selezionare la macchina virtuale e vedere lo stato della replica.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image12.png)

In questo caso lo *stato della replica* viene indicato come critico; *Visualizzare lo stato della replica* per visualizzare i dettagli.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Per casi in cui la replica è in pausa per la macchina virtuale, fare clic con il pulsante destro del mouse su *Replica*->*Riprendi replica* ![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Nel caso in cui venga eseguita la migrazione della macchina virtuale su un nuovo host Hyper-V (all'interno del cluster o di un computer autonomo) configurato mediante il ripristino automatico di sistema, non vi sarebbero ripercussioni sulla replica per la macchina virtuale. Assicurarsi che il nuovo host Hyper-V soddisfi tutti i prerequisiti e sia configurato mediante il ripristino automatico di sistema.

### Registro eventi

| Origini eventi | Dettagli |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| **Applicazioni e servizio Logs/Microsoft/VirtualMachineManager/Server/Admin** (Server VMM) | Fornisce log utili per la risoluzione di numerosi problemi diversi relativi a VMM. |
| **Applicazioni e servizio Logs/MicrosoftAzureRecoveryServices/Replication** (Hyper-V Host) | Fornisce log utili per la risoluzione di molti problemi relativi all’agente di servizi di ripristino di Microsoft Azure. <br/> ![Origine evento per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applicazioni e servizio Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Host Hyper-V) | Fornisce log utili per la risoluzione di molti problemi relativi al servizio Azure Site Recovery di Microsoft. <br/> ![Origine evento per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applicazioni e servizio Logs/Microsoft/Windows/Hyper-V-VMM/Admin** (Host Hyper-V) | Fornisce log utili per la risoluzione di molti problemi di gestione di macchine virtuali Hyper-V. <br/> ![Origine evento per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### Opzioni di registrazione di replica Hyper-V

Tutti gli eventi relativi alla Replica Hyper-V vengono registrati nel Registro di Hyper-V-VMMS\\Admin in **Applicazioni e Servizi\\Microsoft\\Windows**. Inoltre, può essere abilitato un registro analitico per Hyper-V a VMM. Per abilitare questo registro, rendere i registri analitici e di debug visibili nel Visualizzatore eventi. Aprire il Visualizzatore eventi, quindi nel **dal menu Visualizza**, fare clic su **Visualizza registri analitici e Debug**.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un registro analitico è visibile in Hyper-V-VMMS

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Quindi, nel riquadro **Actions** fare clic su **Enable Log**. Una volta attivato, viene visualizzato nel **Performance Monitor** come una sessione di traccia eventi disponibile nella cartella **Insiemi agenti di raccolta dati.**

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Per visualizzare le informazioni raccolte, innanzitutto interrompere la sessione di traccia disabilitando il log, quindi salvare il registro e aprirlo nuovamente nel Visualizzatore eventi o utilizzare altri strumenti per convertirlo come desiderato.



## Contattare il supporto Microsoft

### Raccolta registri

Per la protezione del sito di VMM, fare riferimento [raccolta del Registro di ripristino automatico di sistema utilizzando lo strumento della piattaforma di diagnostica supporto (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) per raccogliere i log necessari.

Per la protezione dei siti di Hyper-V, scaricare lo [strumento](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) ed eseguirlo nell'host Hyper-V per raccogliere i log.

Per gli scenari di VMware/fisici, fare riferimento a [Azure Site Recovery Log Collection for VMware and Physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) per raccogliere le informazioni necessarie.

Lo strumento raccoglie i log in locale in una sottocartella denominata in modo casuale **%LocalAppData%\\ElevatedDiagnostics**

![Procedure di esempio illustrate dalla protezione dei siti Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### Apertura di un ticket di supporto

Per generare il ticket di supporto per il ripristino automatico di sistema, raggiungere per il supporto di Azure utilizzando l'URL in <http://aka.ms/getazuresupport>

## Articoli correlati

-   [Come mantenere la lettera di unità per le macchine virtuali protette di cui è stato eseguito il failover o la migrazione in Azure](http://support.microsoft.com/kb/3031135)
-   [Come gestire le impostazioni locali per l'uso della larghezza di banda della rete di protezione di Azure](https://support.microsoft.com/kb/3056159)
-   [Ripristino automatico di sistema: errore "Impossibile trovare la risorsa cluster" quando si tenta di abilitare la protezione per una macchina virtuale](http://support.microsoft.com/kb/3010979)
-   [Guida alla comprensione e alla risoluzione dei problemi relativi alla replica Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## Errori comuni del ripristino automatico di sistema e relative soluzioni

Di seguito sono riportati gli errori comuni che è possibile ricevere e le relative soluzioni. Ogni errore è documentato in una pagina WIKI separata.

### Generale
-   <span style="color:green;">NUOVO</span> [Processi con esito negativo con l’errore "un'operazione è in corso". Error 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">NUOVO</span> [Processi con esito negativo con l’errore "il server non è connesso a Internet". Error 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### Impostazione
-   [Impossibile registrare il server VMM a causa di un errore interno. Fare riferimento alla visualizzazione processi nel portale di Ripristino sito per ulteriori informazioni sull'errore. Eseguire nuovamente l'installazione per registrare il server.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Impossibile stabilire una connessione all'insieme di credenziali per la gestione del ripristino Hyper-V. Verificare le impostazioni del proxy o riprovare più tardi.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Configurazione
-   [Impossibile creare il gruppo protezione dati: si è verificato un errore durante il recupero dell'elenco di server.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Il cluster host Hyper-V contiene almeno una scheda di rete statica oppure nessuna scheda collegata è configurata per utilizzare DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM non dispone delle autorizzazioni necessarie per completare un'azione](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Impossibile selezionare l'account di archiviazione nella sottoscrizione durante la configurazione della protezione](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### Protezione
- <span style="color:green;">NUOVO</span> [L’abilitazione della protezione ha esito negativo con l'errore "Impossibile configurare la protezione per la macchina virtuale". Error 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">NUOVO</span> [L’abilitazione della protezione ha esito negativo con l'errore "Impossibile abilitare la protezione per la macchina virtuale." Error 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">NUOVO</span> [Errore di migrazione in tempo reale 23848 - La macchina virtuale sta per essere spostata utilizzando il tipo Live. Questo potrebbe interrompere lo stato di protezione del ripristino della macchina virtuale.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [L’abilitazione della protezione non è riuscita perché l'agente non è installato nel computer host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Impossibile trovare un host adatto per la macchina virtuale di replica a causa delle scarse risorse di calcolo](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Impossibile trovare un host adatto per la macchina virtuale di replica a causa dell’assenza di una rete logica collegata](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Impossibile connettersi al computer host di replica. La connessione non è stata stabilita](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### Ripristino
- VMM non è in grado di completare l'operazione di host
    -   [Failover sul punto di ripristino selezionato per la macchina virtuale: errore generico di accesso negato.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V: impossibile eseguire il failover al punto di ripristino selezionato per la macchina virtuale. L'operazione è stata interrotta. Provare un punto di ripristino più recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Impossibile stabilire una connessione con il server (0x00002EFD)
        -   [Hyper-V: impossibile abilitare la replica inversa per la macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-V: impossibile abilitare la replica per la macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Impossibile eseguire il commit del failover per la macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Il piano di ripristino contiene macchine virtuali che non sono pronte per il failover pianificato](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [La macchina virtuale non è pronta per il failover pianificato](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [La macchina virtuale non è in esecuzione e non è spenta](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Si è verificata un'operazione fuori banda in una macchina virtuale e il failover del commit non è riuscito](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Failover di test
    -   [Impossibile avviare il failover perché il failover di test è in corso](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

### Server di configurazione, server di elaborazione, server master di destinazione
Server di configurazione (CS), Server di elaborazione (PS), Destinazione master (MT): 
-   [l'host ESXi in cui è ospitato PS/CS come macchina virtuale presenta un errore con schermata viola.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### Risoluzione dei problemi del desktop remoto dopo il failover
-   Molti clienti hanno avuto problemi a connettersi a una macchina virtuale in Azure dopo il failover. [Usare il documento sulla risoluzione dei problemi per effettuare una connessione tramite protocollo RDP alla macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

<!-----HONumber=AcomDC_1217_2015-->