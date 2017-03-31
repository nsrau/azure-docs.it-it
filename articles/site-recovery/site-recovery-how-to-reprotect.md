---
title: 'Procedura: eseguire la riprotezione da Azure al sito locale | Microsoft Docs'
description: "Dopo il failover delle macchine virtuali in Azure, è possibile avviare il failback per riportarle in locale. Imparare i passaggi relativi alla riprotezione prima di un failback."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: f2da9c4b02f0bb8a93347c05be358516f39e2df0
ms.lasthandoff: 03/09/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>Riprotezione da Azure al sito locale

## <a name="overview"></a>Panoramica
Questo articolo descrive come eseguire la riprotezione di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-failover.md).

Dopo il completamento della riprotezione e l'avvio della replica delle macchine virtuali protette, è possibile avviare un failback nella macchina virtuale per portare anche quelle in locale.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Per una rapida panoramica video, guardare il video seguente.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

Informazioni sul processo completo di failback.

## <a name="pre-requisites"></a>Prerequisiti
Di seguito vengono riportati i passaggi prerequisiti da eseguire o prendere in considerazione quando ci si prepara per la riprotezione.

* Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, assicurarsi di avere le autorizzazioni necessarie per l'individuazione di macchine virtuali nei server vCenter. [Altre informazioni](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se in una macchina virtuale locale sono presenti snapshot la riprotezione avrà esito negativo. È possibile eliminare gli snapshot prima di continuare con la riprotezione.
* Prima di eseguire il failback è necessario creare due componenti aggiuntivi:
  * **Creare un server di elaborazione**. Il server di elaborazione viene usato per ricevere i dati dalla macchina virtuale protetta in Azure e inviarli in locale. Deve quindi trovarsi in una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta. Pertanto, il server di elaborazione può essere locale se si usa una connessione ExpressRoute o in Azure si usa una VPN.
  * **Creare un server di destinazione master**: il server di destinazione riceve i dati di failback. Nel server di gestione creato in locale è installato un server di destinazione master per impostazione predefinita. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback.
        * [Una macchina virtuale Linux necessita di un server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md).
        * Una macchina virtuale Windows richiede una destinazione master Windows. È possibile riutilizzare la macchina PS+MT locale.
* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione, altrimenti il failback non avrà esito positivo. Assicurarsi pertanto di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
* Assicurarsi di configurare l'impostazione disk.enableUUID=true nei parametri di configurazione della VM di destinazione master in VMware. Se questa riga non esiste, aggiungerla perché è necessaria per fornire un valore UUID coerente al file VMDK in modo che venga installato correttamente.
* **Al server di destinazione master non può essere applicato Storage vMotion**. perché può impedire il completamento del failback. La VM non verrà attivata perché i dischi non risulteranno disponibili.
* È necessaria una nuova unità aggiunta al server di destinazione master. Questa unità è denominata unità di conservazione. Aggiungere un nuovo disco e formattare l'unità.
* La destinazione master ha altri prerequisiti elencati in [Elementi comuni da controllare dopo aver installato un server di destinazione master](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>Perché è necessaria una VPN S2S o un circuito ExpressRoute per replicare nuovamente i dati in locale?
La replica dal sito locale ad Azure può avere luogo tramite Internet o un circuito ExpressRoute con peering pubblico, ma la riprotezione e il failback richiedono la configurazione di una VPN S2S per la replica dei dati. **La rete deve essere configurata per permettere alle macchine virtuali con failover in Azure di raggiungere (ping) il server di configurazione locale**. Si potrebbe anche voler distribuire un server di elaborazione nella rete di Azure della macchina virtuale con failover. Questo server deve essere anche in grado di comunicare con il server di configurazione locale.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando si deve installare un server di elaborazione in Azure?


Inviare i dati di replica delle macchine virtuali di Azure da riproteggere a un server di elaborazione. La rete deve essere configurata per permettere di raggiungere il server di elaborazione da una macchina virtuale di Azure.

È possibile distribuire un server di elaborazione in Azure o usare quello esistente usato per il failover. Il punto importante da considerare è la latenza per inviare i dati da macchine virtuali di Azure al server di elaborazione.

* Se si dispone di un circuito ExpressRoute, è possibile usare un server di elaborazione locale per inviare i dati. In questo modo la latenza tra la macchina virtuale e il server di elaborazione sarà limitata.

    ![Diagramma dell'architettura per Expressroute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* Tuttavia, se si dispone solo di una VPN S2S, è consigliabile distribuire il server di elaborazione in Azure.

    ![Diagramma dell'architettura per VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Tenere presente che la replica avrà luogo solo tramite VPN S2S o tramite il peering privato della rete ExpressRoute. Assicurarsi che sia presente una larghezza di banda sufficiente nel canale di rete.

Altre informazioni su come installare un [server di elaborazione di Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Quali sono le diverse porte da aprire per i diversi componenti in modo che la riprotezione possa funzionare?

![Failover-Failback - Tutte le porte](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>Quale server master di destinazione usare per la riprotezione?
Un server di destinazione master è richiesto in locale per ricevere i dati dal server di elaborazione e quindi scrivere in VMDK della macchina virtuale locale. Se si desidera proteggere macchine virtuali Windows, è necessario un server di destinazione master Windows in cui riusare il server di elaborazione e il server di destinazione master locali <!-- !todo component -->. Per le macchine virtuali Linux, è necessario configurare un server di destinazione master Linux locale aggiuntivo.


Fare clic sui collegamenti seguenti per conoscere i passaggi su come installare un server di destinazione master.

* [Come installare un server di destinazione master Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Come installare un server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Elementi comuni da controllare dopo aver installato un server di destinazione master

* Se la macchina virtuale è presente in locale nel server vCenter, il server di destinazione master deve poter accedere a VMDK nella macchina virtuale locale. Questo per scrivere i dati replicati nei dischi della macchina virtuale. Per farlo è necessario assicurarsi che **l'archivio dati della macchina virtuale locale sia montato nell'host del server di destinazione master con accesso in lettura e scrittura**.

* Se la macchina virtuale non è presente in locale nel server vCenter, sarà necessario creare una nuova macchina virtuale durante la riprotezione. Questa macchina virtuale verrà creata nell'host ESX in cui si crea il server di destinazione master. Di conseguenza, scegliere con attenzione l'host ESX in modo che la macchina virtuale di failback venga creata nell'host desiderato.

* **Al server di destinazione master non può essere applicato Storage vMotion**. perché può impedire il completamento del failback. La VM non verrà attivata perché i dischi non risulteranno disponibili.

* È necessaria una nuova unità aggiunta al server di destinazione master Windows esistente. Questa unità è denominata unità di conservazione. Aggiungere un nuovo disco e formattare l'unità. Per arrestare i punti temporali in cui la VM viene replicata nel sito locale viene usata un’unità di conservazione. Di seguito sono indicati alcuni criteri per un’unità di conservazione, senza i quali l’unità non sarà elencata per il server master di destinazione.

   * Il volume non deve essere in uso per altri scopi (destinazione di replica e così via).

   * Il volume non deve essere in modalità di blocco

   * Il volume non deve essere il volume della cache. Nel volume non deve esistere un'installazione MT. Il volume di installazione personalizzata PS+MT non è idoneo per il volume di conservazione. Il volume PS+MT qui installato è il volume della cache di MT.

   * Il file system del volume non deve essere di tipo FAT e FAT32.

   * La capacità del volume deve essere diversa da zero.

   * Il volume di conservazione predefinito per Windows è il volume R.

   * Il volume di conservazione predefinito per Linux è /mnt/retention/.

* Una macchina virtuale Linux con failover necessita di un nuovo server di destinazione master Linux. Una macchina virtuale Windows con failover necessita di un nuovo server di destinazione master Windows.

* Installare gli strumenti VMware nel server di destinazione master. Senza gli strumenti VMware, non è possibile rilevare gli archivi dati nell'host ESXi del server di destinazione master.

* Abilitare il parametro disk.EnableUUID = True nella macchina virtuale del server di destinazione master tramite le proprietà di vCenter. <!-- !todo Needs link. -->

* La destinazione master deve avere almeno un archivio dati VMFS collegato. Se non è disponibile, l'input dell'archivio dati sulla pagina di riprotezione sarà vuoto e non sarà possibile continuare.

* Il server di destinazione master non può avere snapshot dei dischi. Se sono presenti snapshot, la riprotezione o il failback non riuscirà.

* MT non può essere un controller SCSI Paravirtual. Può essere solo un controller LSI Logic. Senza un controller LSI Logic, la riprotezione non riuscirà.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Passaggi della riprotezione

Prima della riprotezione, verificare che siano installati il [server di elaborazione](site-recovery-vmware-setup-azure-ps-resource-manager.md) in Azure e il server Windows o il [server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md) locale.

> [!NOTE]
> Dopo l'avvio di una VM in Azure, registrare di nuovo l'agente nel server di configurazione può richiedere tempo (fino a 15 minuti). Durante questo intervallo di tempo la riprotezione non riuscirà e sarà visualizzato un messaggio di errore con l'avviso che l'agente non è installato. Attendere alcuni minuti e ripetere l'operazione di riprotezione.



1. In Insieme di credenziali selezionare negli elementi replicati la VM di cui è stato eseguito il failover e fare clic con il pulsante destro su **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare la riprotezione dai pulsanti di comando.
2. Nel pannello, si può osservare che la direzione della protezione “da Azure al sito locale” è già selezionata.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.
4. Selezionare l' **archivio dati** in cui ripristinare i dischi in locale. Questa opzione viene utilizzata quando la VM locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione.
6. I criteri di failback verranno selezionati automaticamente.
7. Dopo aver fatto clic su **OK** per avviare la riprotezione, inizia un processo di replica della VM da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

Per eseguire il ripristino in un percorso alternativo (quando viene eliminata la macchina virtuale locale), selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Verrà creata una nuova macchina virtuale in vCenter.
Se si desidera ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente, è necessario montare gli archivi dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

È anche possibile eseguire la riprotezione a livello di piano di ripristino. Se si dispone di un gruppo di replica, può essere riprotetto solo usando un piano di ripristino. Durante la riprotezione tramite un piano di ripristino, è necessario assegnare i valori sopra riportati per ogni computer protetto.

> [!NOTE]
> Un gruppo di replica deve essere protetto con la stessa destinazione master. In caso di protezione con un server di destinazione master diverso, non è possibile accettare una temporizzazione comune.


Dopo la riprotezione, la macchina virtuale entrerà in uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

Una volta in questo stato, è possibile avviare un failback. Il failback arresterà la macchina virtuale in Azure e avvierà quella locale. Ci sarà pertanto un breve tempo di inattività per l'applicazione. Selezionare quindi un'ora di failback in cui l'inattività dell'applicazione non causerà conseguenze.

[Passaggi per avviare il failback della macchina virtuale](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>Problemi comuni

* Se le macchine virtuali sono state create usando un modello, prima è necessario assicurarsi che ogni VM abbia un UUID univoco per i dischi. Se l'UUID della VM locale è in conflitto con quello del master di destinazione (poiché entrambi sono stati creati dallo stesso modello), la riprotezione non riesce. È necessario distribuire un altro server di destinazione master che non sia stato creato dallo stesso modello.
* Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, il processo funziona e il failover viene eseguito. Durante la riprotezione, failover ha esito negativo perché non è possibile individuare gli archivi dati. Non verranno quindi visualizzati gli archivi dati elencati durante la riprotezione. Per risolvere questo problema, è possibile aggiornare le credenziali di vCenter usando un account appropriato che abbia le autorizzazioni, quindi ripetere il processo. Per altre informazioni, vedere [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si nota che il pacchetto di gestione reti viene disinstallato dal computer. Tale installazione si verifica perché, quando la macchina virtuale viene ripristinata in Azure, il pacchetto di gestione reti viene rimosso.
* Se per la configurazione di una VM Linux viene usato un indirizzo IP statico e viene eseguito il failover in Azure, l'indirizzo IP viene acquisito tramite DHCP. Quando il failover viene eseguito di nuovo in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e impostare l'indirizzo IP torna su un indirizzo statico. Una VM Windows può acquisire nuovamente il proprio indirizzo IP statico.
* Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, sarà possibile eseguire il failover, ma non eseguire il failback. Per abilitare il failback, scegliere l'aggiornamento alla licenza di valutazione del programma.
* Se non è possibile raggiungere il server di configurazione dal server di elaborazione, verificare la connettività al server di configurazione, da Telnet al computer del server di configurazione sulla porta 443. È anche possibile provare a eseguire il ping del server di configurazione dal computer del server di elaborazione. Un server di elaborazione deve avere anche un heartbeat quando è connesso al server di configurazione.
* Se si sta tentando di eseguire il failback a un vCenter alternativo, assicurarsi che il nuovo vCenter e il server di destinazione master vengano rilevati. Come sintomo tipico, gli archivi dati non sono accessibili o visibili nella finestra di dialogo **Riproteggi**.
* Non è possibile eseguire il failback da Azure a locale di un computer WS2008R2SP1 protetto come computer locale fisico.

