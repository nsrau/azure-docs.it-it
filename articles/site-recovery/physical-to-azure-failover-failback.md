---
title: Failover e failback dei server fisici replicati in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover dei server fisici in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: bbad2a0ea1a58834eaf32e0d3286f6e8a794d364
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Failover e failback dei server fisici replicati in Azure

Questa esercitazione descrive come eseguire il failover di un server fisico in Azure. Dopo il failover verrà eseguito il failback del server nel sito locale quando risulterà disponibile. 

## <a name="preparing-for-failover-and-failback"></a>Preparazione per il failover e il failback

I server fisici replicati in Azure con Site Recovery possono eseguire il failback solo come VM VMware. È quindi necessaria un'infrastruttura VMware per eseguire il failback. 

L'operazione di failover e failback comprende quattro fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Riprotezione delle VM di Azure**: riproteggere le macchine virtuali di Azure in modo da avviarne la replica nelle VM VMware locali.
3. **Failover nel sito locale**: eseguire un failover per il failback da Azure.
4. **Riprotezione delle VM locali**: dopo il failback dei dati, riproteggere le VM VMware locali in cui è stato eseguito il failback in modo da avviarne la replica in Azure.

## <a name="verify-server-properties"></a>Verificare le proprietà del server

Verificare le proprietà del server e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) per le VM di Azure.

1. In **Elementi protetti** fare clic su **Elementi replicati** e selezionare il computer.

2. Nel riquadro **Replicated item** (Elemento replicato) è possibile vedere un riepilogo relativo a informazioni sulla macchina, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le [impostazioni del disco gestito](#managed-disk-considerations)
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Disks** (Dischi) è possibile vedere le informazioni sul sistema operativo e sui dischi dati della macchina.

## <a name="run-a-failover-to-azure"></a>Eseguire un failover in Azure

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente** (predefinita): questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Latest processed** (Elaborato più recente): questa opzione consente di eseguire il failover della macchina nel punto di recupero più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Latest app-consistent** (Coerente con l'app più recente): questa opzione esegue il failover della macchina nel punto di recupero coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) se si vuole provare ad arrestare la macchina di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Se la connessione alla macchina virtuale di Azure è stata preparata, connettersi per convalidarla dopo il failover.
5. Dopo la verifica, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> Non annullare un failover in corso. Prima di iniziare il failover, la replica della macchina si arresta. Se si annulla il failover questo si arresta, ma la macchina non eseguirà nuovamente la replica.
> Per i server fisici, il completamento dell'elaborazione di un failover aggiuntivo può richiedere da 8 a 10 minuti circa. 

## <a name="create-a-process-server-in-azure"></a>Creare un server di elaborazione in Azure

Il server di elaborazione riceve i dati dalla macchina virtuale di Azure e li invia al sito locale. È necessaria una rete a bassa latenza tra il server di elaborazione e la macchina protetta.

- A scopo di test, se si ha una connessione Azure ExpressRoute è possibile usare il server di elaborazione locale che viene installato automaticamente nel server di configurazione.
- Se si ha una connessione VPN o si esegue il failback in un ambiente di produzione è necessario configurare una macchina virtuale di Azure come server di elaborazione basato su Azure per il failback.
- Per configurare un server di elaborazione in Azure, seguire le istruzioni fornite in [questo articolo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configurare il server di destinazione master

Per impostazione predefinita, il server di destinazione master riceve i dati di failback. Viene eseguito sul server di configurazione locale.

- Se la VM VMware di cui si esegue il failback si trova in un host ESXi gestito da un server VMware vCenter è necessario che il server di destinazione master abbia accesso all'archivio dati della VM (VMDK) per poter scrivere i dati replicati nei dischi della VM. Verificare che l'archivio dati della macchina virtuale sia montato nell'host del server di destinazione master con accesso in lettura/scrittura.
- Se la macchina virtuale si trova in un host ESXi non gestito da un server vCenter, il servizio Site Recovery crea una nuova VM durante la riprotezione. La VM viene creata nell'host ESX in cui si crea la VM di destinazione master. Il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile dall'host in cui viene eseguito il server di destinazione master.
- Per le macchine fisiche di cui si esegue il failback è necessario completare l'individuazione dell'host in cui viene eseguito il server di destinazione master prima di riproteggere la macchina.
- Un'altra opzione, in presenza della VM locale per il failback, consiste nell'eliminarla prima di eseguire un failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESX di destinazione master. Quando si esegue il failback in una posizione alternativa, i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESX usati dal server di destinazione master locale.
- Non è possibile usare Storage vMotion nel server di destinazione master. Se lo si usa, il failback non funzionerà perché i dischi non risulteranno disponibili. Escludere i server di destinazione master dall'elenco vMotion.

## <a name="reprotect-azure-vms"></a>Riproteggere le macchine virtuali di Azure

Questa procedura presuppone che la macchina virtuale locale non sia disponibile e che la riprotezione venga eseguita in un percorso alternativo.

1. In **Impostazioni** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover > **Riproteggi**.
2. In **Riproteggi** verificare che **Da Azure a locale** sia selezionato.
3. Specificare il server di destinazione master locale e il server di elaborazione.

4. In **Archivio dati** selezionare l'archivio dati del server di destinazione master in cui ripristinare i dischi in locale. Usare questa opzione quando la macchina virtuale locale è stata eliminata ed è necessario creare nuovi dischi. Questa impostazione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione del server di destinazione master. I criteri di failback vengono selezionati automaticamente.
6. Fare clic su **OK** per avviare la riprotezione. Avrà inizio un processo di replica della macchina virtuale da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

> [!NOTE]
> Se si desidera ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente montare l'archivio dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Eseguire un failover da Azure nel sito locale

Per eseguire la replica locale si usano criteri di failback. Questi criteri sono stati creati automaticamente al momento della creazione dei criteri di replica per la replica in Azure:

- I criteri vengono automaticamente associati al server di configurazione.
- I criteri non possono essere modificati.
- I valori dei criteri sono:
    - Soglia RPO: 15 minuti
    - Conservazione del punto di ripristino: 24 ore
    - Frequenza snapshot coerenti con l'app: 60 minuti

Eseguire il failover come descritto di seguito:

1. Nella pagina **Elementi replicati** fare clic con il pulsante destro del mouse sul computer > **Failover non pianificato**.
2. In **Conferma failover** verificare che la direzione di failover sia da Azure.

3. Selezionare il punto di ripristino da usare per il failover. Il punto di ripristino coerente con l'applicazione si verifica prima del punto più recente nel tempo e provoca perdita di dati. Quando viene eseguito il failover, Site Recovery arresta le macchine virtuali di Azure e avvia la macchina virtuale locale. L'operazione comporta un tempo di inattività. Scegliere pertanto un orario appropriato.
4. Fare clic con il pulsante destro del mouse sul computer e quindi su **Esegui commit**. Verrà attivato un processo che rimuove le macchine virtuali di Azure.
5. Verificare che le macchine virtuali di Azure siano state arrestate come previsto.


## <a name="reprotect-on-premises-machines-to-azure"></a>Riproteggere i computer locali in Azure

I dati dovrebbero ora essere tornati nel sito locale, ma non vengono replicati in Azure. È possibile avviare nuovamente la replica in Azure seguendo questa procedura:

1. Nell'insieme di credenziali > **Impostazioni** >**Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Selezionare il server di elaborazione usato per inviare i dati replicati in Azure e fare clic su **OK**.

Al termine della riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire un failover in base alle esigenze.

