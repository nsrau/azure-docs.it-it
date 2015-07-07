<properties
	pageTitle="Guida al monitoraggio e alla risoluzione dei problemi di VMM e di protezione dei siti di Hyper-V" 
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
	ms.date="06/16/2015" 
	ms.author="anbacker"/>
	
# Guida al monitoraggio e alla risoluzione dei problemi di VMM e di protezione dei siti di Hyper-V

La Guida al monitoraggio e la alla risoluzione dei problemi consente come monitorare e apprendere lo stato della replica di rilevamento e risoluzione dei problemi di tecniche per Azure Site Recovery.

## Informazioni sui componenti

### Distribuzione del sito VMM per la replica tra siti locali.

Nell'ambito dell'impostazione di ripristino di emergenza tra due siti locali; il provider di Azure Site Recovery deve essere scaricato e installato nel server VMM. ll provider richiede la connettività a Internet per garantire che tutte le operazioni attivate dal portale di Azure vengano convertite in operazioni locali, tra cui l'abilitazione della protezione, l'arresto delle macchine virtuali primarie nell'ambito del failover e così via.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image1.png)

### Distribuzione del sito VMM per la replica tra siti locali e Azure.

Nell'ambito dell'impostazione di ripristino di emergenza tra due siti locali e Azure; il provider di Azure Site Recovery deve essere scaricato e installato nel server VMM insieme all'agent di Azure Recovery Services che deve essere installato in ciascun host Hyper-V.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image2.png)

### Distribuzione del sito Hyper-V per la replica tra siti locali e Azure.

Si tratta della stessa situazione di una distribuzione VMM; l'unica differenza è dovuta al fatto che il provider e l'agent vengono installati nell'host Hyper-V.

## Monitorare la configurazione, la protezione e il ripristino

Ogni operazione in ASR viene controllata e monitorata nella scheda "JOBS". In caso di eventuali errori di configurazione, protezione o ripristino, passare alla scheda JOBS e verificare se vi sono errori.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image3.png)

Dopo aver individuato gli errori nella visualizzazione PROCESSI, selezionare il processo e fare clic su DETTAGLI ERRORE per tale processo.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image4.png)

I dettagli errore consentiranno di identificare eventuali cause e suggerimenti per il problema.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image5.png)

Nel caso sopra sembra esserci un'altra operazione in corso a causa della quale la configurazione di protezione ha esito negativo. Assicurarsi di risolvere il problema in base all'indicazione fornita; successivamente fare clic su RIAVVIA per riavviare l'operazione.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image6.png)

L'opzione per RIAVVIARE non è sempre disponibile per tutte le operazioni; coloro che non dispongono dell'opzione RIAVVIA devono tornare all'oggetto e riavviare l'operazione. Ogni PROCESSO può essere annullato in qualsiasi momento utilizzando il pulsante di annullamento in corso.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image7.png)

## Monitorare lo stato della replica per la macchina virtuale

Provider di ripristino automatico di sistema centrale e di monitoraggio tramite il portale di Azure per ogni entità protetta. Passare alla scheda ELEMENTI PROTETTI e selezionare CLOUD VMM o GRUPPI DI PROTEZIONE. Scheda CLOUD VMM è solo per le distribuzioni di tipo VMM e tutti gli altri scenari avere entità protette nella scheda GRUPPI PROTEZIONE dati.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image8.png)

Successivamente, selezionare l'entità protetta sotto il rispettivo cloud o il gruppo protezione dati. Dopo aver selezionato l'entità protetta, tutte le operazioni consentite vengono visualizzate nel riquadro inferiore.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image9.png)

Come illustrato in precedenza, nel caso in cui lo l'integrità della macchina virtuale che integrità è fondamentale: è possibile fare clic sul pulsante i Dettagli dell'errore nella parte inferiore per visualizzare l'errore. In base alle "cause possibili" i suggerimenti indicati risolvono il problema; in questo caso la macchina virtuale deve essere sincronizzata nuovamente, operazione eseguibile dallo stesso portale facendo clic sul pulsante RESYNCHRONIZZE.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image11.png)

Nota: se vi sono operazioni attive in corso o non riuscite, passare alla visualizzazione PROCESSI come indicato in precedenza per visualizzare l'errore specifico del processo.

## Risolvere i problemi a livello locale

Connettersi alla console di gestione di Hyper-V locale, selezionare la macchina virtuale e vedere lo stato della replica.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image12.png)

In questo caso lo *stato della replica* viene indicato come critico; *Visualizzare lo stato della replica* per visualizzare i dettagli.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image13.png)

#### Aprire il Visualizzatore eventi

| Scenari | Origini eventi |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Protezione sito VMM | VMM Server <ul><li> **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Hyper-V Host <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Per Azure come destinazione)</li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Protezione sito Hyper-V | <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** </li><li> **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Opzioni di registrazione di replica Hyper-V

Tutti gli eventi relativi alla Replica Hyper-V vengono registrati nel Registro di Hyper-V-VMMS\Admin in **Applicazioni e Servizi\Microsoft\Windows**. Inoltre, può essere abilitato un registro analitico per Hyper-V a VMM. Per abilitare questo registro, rendere i registri analitici e di debug visibili nel Visualizzatore eventi. Aprire il Visualizzatore eventi, quindi nel **dal menu Visualizza**, fare clic su **Visualizza registri analitici e Debug**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image14.png)

Un registro analitico è visibile in Hyper-V-VMMS

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image15.png)

Quindi, nel riquadro **Actions** fare clic su **Enable Log**. Una volta attivato, viene visualizzato nel **Performance Monitor** come una sessione di traccia eventi disponibile nella cartella **Insiemi agenti di raccolta dati.**

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image16.png)

Per visualizzare le informazioni raccolte, innanzitutto interrompere la sessione di traccia disabilitando il log, quindi salvare il registro e aprirlo nuovamente nel Visualizzatore eventi o utilizzare altri strumenti per convertirlo come desiderato.


## Comprendere il ciclo di vita della macchina virtuale

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image17.png)


## Contattare il supporto Microsoft

### Raccolta registri

Per la protezione del sito di VMM, fare riferimento [raccolta del Registro di ripristino automatico di sistema utilizzando lo strumento della piattaforma di diagnostica supporto (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) per raccogliere i log necessari.

Per Hyper-V Branch Office and SMB Site protection, scaricare lo [strumento](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) ed eseguirlo nell'host Hyper-V per raccogliere i log.

Per gli scenari di VMware/fisici, fare riferimento a [Azure Site Recovery Log Collection for VMware and Physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) per raccogliere le informazioni necessarie.

Lo strumento SDP raccoglie il file di log in locale che è anche possibile individuare in una sottocartella denominata in modo casuale **%LocalAppData%\ElevatedDiagnostics**

### Apertura di un ticket di supporto

Per generare il ticket di supporto per il ripristino automatico di sistema, raggiungere per il supporto di Azure utilizzando l'URL in <http://aka.ms/getazuresupport>

## Articoli correlati

-   [Come conservare la lettera di unità per le macchine virtuali protette]
    > http://support.microsoft.com/kb/3031135

-   [Come risolvere i problemi dei servizi di ripristino
    > di Azure](http://support.microsoft.com/kb/3005185)

-   [Come abilitare la registrazione di Debug per Azure Site Recovery in Hyper-V
    > Site Protection](http://support.microsoft.com/kb/3033922)

-   [ASR: "Impossibile trovare la risorsa cluster" Errore durante il tentativo
    > di abilitare la protezione per una macchina virtuale](http://support.microsoft.com/kb/3010979)
    
-   [Guida a comprendere e risolvere i problemi relativi a Replica
    > Hyper-V](http://www.microsoft.com/it-it/download/details.aspx?id=29016)

<!---HONumber=62-->