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
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: e650439b4bfd468c4bd7b236b80ce7361de1c6ac
ms.openlocfilehash: 972a3e88d15be656fd8cd3d51b7e507c7cbb49d5


---
# <a name="reprotect-from-azure-to-on-premises"></a>Riprotezione da Azure al sito locale

## <a name="overview"></a>Panoramica
Questo articolo descrive come eseguire la riprotezione di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-vmware-to-azure-classic.md).

Dopo il completamento della riprotezione e l'avvio della replica delle macchine virtuali protette, è possibile avviare un failback nella macchina virtuale per portare anche quelle in locale.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Prerequisiti
Di seguito vengono riportati i passaggi prerequisiti da eseguire o prendere in considerazione quando ci si prepara per la riprotezione.

### <a name="reprotect-needs-a-s2s-vpn-or-an-express-route-to-replicate-data-back-to-on-premises"></a>La riprotezione necessita di una VPN S2S o di un circuito ExpressRoute per replicare nuovamente i dati in locale
La replica dal sito locale ad Azure può avere luogo tramite Internet o un circuito ExpressRoute con peering pubblico, ma la riprotezione e il failback richiedono la configurazione di una VPN S2S per la replica dei dati. La rete deve essere configurata per permettere alle macchine virtuali con failover in Azure di raggiungere il server di configurazione locale. Si potrebbe anche voler distribuire un server di elaborazione nella rete di Azure della macchina virtuale con failover. Questo server deve essere anche in grado di comunicare con il server di configurazione locale.

### <a name="process-server-is-needed-to-replicate-the-data-from-source-vms-to-on-premises"></a>Il server di elaborazione è necessario per replicare i dati dalle macchine virtuali di origine al sito locale
<!-- Read more about a process server here.!todo -->

Inviare i dati di replica delle macchine virtuali di Azure da riproteggere a un server di elaborazione. La rete deve essere configurata per permettere di raggiungere il server di elaborazione da una macchina virtuale di Azure.

È possibile distribuire un server di elaborazione in Azure o usare quello esistente usato per il failover. Il punto importante da considerare è la latenza per inviare i dati da macchine virtuali di Azure al server di elaborazione. 

* Se si dispone di un circuito ExpressRoute, è possibile usare un server di elaborazione locale per inviare i dati. In questo modo la latenza tra la macchina virtuale e il server di elaborazione sarà limitata.
    
    ![Diagramma dell'architettura per Expressroute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* Tuttavia, se si dispone solo di una VPN S2S, è consigliabile distribuire il server di elaborazione in Azure.

    ![Diagramma dell'architettura per VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Tenere presente che la replica avrà luogo solo tramite VPN S2S o tramite il peering privato della rete ExpressRoute. Assicurarsi che sia presente una larghezza di banda sufficiente nel canale di rete.

### <a name="ports-to-be-opened-on-different-machines-so-that-all-the-components-can-communicate"></a>Porte da aprire in computer diversi, in modo che tutti i componenti possono comunicare

![Failover-Failback - Tutte le porte](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="master-target-needs-to-be-available-on-premises-to-receive-data-from-process-server"></a>Il server di destinazione master deve essere disponibile in locale per ricevere i dati dal server di elaborazione
Un server di destinazione master è richiesto in locale per ricevere i dati dal server di elaborazione e quindi scrivere in VMDK della macchina virtuale locale. Se si desidera proteggere macchine virtuali Windows, è necessario un server di destinazione master Windows in cui riusare il server di elaborazione e il server di destinazione master locali <!-- !todo component -->. Per le macchine virtuali Linux, è necessario configurare un server di destinazione master Linux locale aggiuntivo.

Fare clic sui collegamenti seguenti per conoscere i passaggi su come installare un server di destinazione master.

* [Come installare un server di destinazione master Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Come installare un server di destinazione master Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Elementi comuni da controllare dopo aver installato un server di destinazione master

* Se la macchina virtuale è presente in locale nel server vCenter, il server di destinazione master deve poter accedere a VMDK nella macchina virtuale locale. Questo per scrivere i dati replicati nei dischi della macchina virtuale. Per farlo è necessario assicurarsi che **l'archivio dati della macchina virtuale locale sia montato nell'host del server di destinazione master con accesso in lettura e scrittura**.

* Se la macchina virtuale non è presente in locale nel server vCenter, sarà necessario creare una nuova macchina virtuale durante la riprotezione. Questa macchina virtuale verrà creata nell'host ESX in cui si crea il server di destinazione master. Di conseguenza, scegliere con attenzione l'host ESX in modo che la macchina virtuale di failback venga creata nell'host desiderato.
    
* **Al server di destinazione master non può essere applicato Storage vMotion**. perché può impedire il completamento del failback. La VM non verrà attivata perché i dischi non risulteranno disponibili.

* È necessaria una nuova unità aggiunta al server di destinazione master esistente. Questa unità è denominata unità di conservazione. Aggiungere un nuovo disco e formattare l'unità. Per arrestare i punti temporali in cui la VM viene replicata nel sito locale viene usata un’unità di conservazione. Di seguito sono indicati alcuni criteri per un’unità di conservazione, senza i quali l’unità non sarà elencata per il server master di destinazione.
   
   a. Il volume non deve essere in uso per altri scopi (destinazione di replica e così via).

   b. Il volume non deve essere in modalità di blocco

   c. Il volume non deve essere il volume della cache. Nel volume non deve esistere un'installazione MT. Il volume di installazione personalizzata PS+MT non è idoneo per il volume di conservazione. Il volume PS+MT qui installato è il volume della cache di MT.

   d. Il file system del volume non deve essere di tipo FAT e FAT32.

   e. La capacità del volume deve essere diversa da zero.

   e. Il volume di conservazione predefinito per Windows è il volume R.

   f. Il volume di conservazione predefinito per Linux è /mnt/retention/.

* Una macchina virtuale Linux con failover necessita di un nuovo server di destinazione master Linux. Una macchina virtuale Windows con failover necessita di un nuovo server di destinazione master Windows.

* Installare gli strumenti VMware nel server di destinazione master. Senza gli strumenti VMware, non è possibile rilevare gli archivi dati nell'host ESXi del server di destinazione master.

* Abilitare il parametro disk.EnableUUID = True nella macchina virtuale del server di destinazione master tramite le proprietà di vCenter. <!-- !todo Needs link. -->



### <a name="failback-policy"></a>Criteri di failback
Per eseguire la replica locale, sono necessari criteri di failback. Questi criteri vengono creati automaticamente quando si creano i criteri di direzione in avanti. Si noti che:

1. Questi criteri vengo associati automaticamente al server di configurazione durante la creazione.
2. Questi criteri non sono modificabili.
3. I valori impostati dei criteri sono: Soglia RPO = 15 minuti, Conservazione del punto di ripristino = 24 ore, Frequenza snapshot per la coerenza con l'app = 60 minuti ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)



## <a name="steps-to-reprotect"></a>Passaggi della riprotezione

1. In Insieme di credenziali selezionare negli elementi replicati la VM di cui è stato eseguito il failover e fare clic con il pulsante destro su **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare la riprotezione dai pulsanti di comando.
2. Nel pannello, si può osservare che la direzione della protezione “da Azure al sito locale” è già selezionata.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.
4. Selezionare l' **archivio dati** in cui ripristinare i dischi in locale. Questa opzione viene utilizzata quando la VM locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione. 
6. I criteri di failback verranno selezionati automaticamente.
7. Dopo aver fatto clic su **OK** per avviare la riprotezione, inizia un processo di replica della VM da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

Per eseguire il ripristino in un percorso alternativo (quando viene eliminata la macchina virtuale locale), selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Verrà creata una nuova macchina virtuale in vCenter. Se si desidera ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente, è necessario montare gli archivi dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

È anche possibile eseguire la riprotezione a livello di piano di ripristino. Se si dispone di un gruppo di replica, può essere riprotetto solo usando un piano di ripristino. Durante la riprotezione tramite un piano di ripristino, è necessario assegnare i valori sopra riportati per ogni computer protetto.

> [!NOTE]
> Un gruppo di replica deve essere protetto con la stessa destinazione master. In caso di protezione con un server di destinazione master diverso, non è possibile accettare una temporizzazione comune.
> 
> 

Dopo la riprotezione, la macchina virtuale entrerà in uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

Una volta in questo stato, è possibile avviare un failback. Il failback arresterà la macchina virtuale in Azure e avvierà quella locale. Ci sarà pertanto un breve tempo di inattività per l'applicazione. Selezionare quindi un'ora di failback in cui l'inattività dell'applicazione non causerà conseguenze.

[Passaggi per avviare il failback della macchina virtuale](site-recovery-how-to-failback-v2a.md#steps-to-failback)

## <a name="common-issues"></a>Problemi comuni 



<!--HONumber=Feb17_HO2-->


