---
title: Preparare le macchine virtuali VMware per la riprotezione e il failback con Azure Site RecoveryPrepare VMware VMs for reprotection and failback with Azure Site Recovery
description: Preparare il failback delle macchine virtuali VMware dopo il failover con Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257186"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Preparare la riprotezione e il failback delle macchine virtuali VMwarePrepare for reprotection and failback of VMware VMs

Dopo il [failover](site-recovery-failover.md) delle macchine virtuali VMware locali o dei server fisici in Azure, è possibile riproteggere le macchine virtuali di Azure create dopo il failover, in modo che vengono replicate nel sito locale. Con la replica da Azure in locale, è quindi possibile eseguire il failback eseguendo un failover da Azure a locale quando si è pronti.

Prima di continuare, ottenere una rapida panoramica di questo video su come eseguire il failback da Azure a un sito locale.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Componenti di riprotezione/failback

Prima di poter riproteggere ed eseguire il failback da Azure sono necessari numerosi componenti e impostazioni.

**Componente**| **Dettagli**
--- | ---
**Server di configurazione locale** | Il server di configurazione locale deve essere in esecuzione e connesso ad Azure.The on-premises configuration server must be running and connected to Azure.<br/><br/> La macchina virtuale in cui si sta esimdo deve esistere nel database del server di configurazione. Se l'emergenza interessa il server di configurazione, ripristinarlo con lo stesso indirizzo IP per assicurarsi che il failback funzioni.<br/><br/>  Se gli indirizzi IP dei computer replicati sono stati mantenuti in caso di failover, è necessario stabilire la connettività da sito a sito (o connettività ExpressRoute) tra i computer delle macchine virtuali di Azure e la scheda di interfaccia di rete di failback del server di configurazione. Per gli indirizzi IP mantenuti, il server di configurazione necessita di due schede di interfaccia di rete, una per la connettività del computer di origine e una per la connettività di failback di Azure.For retained IP addresses the configuration server needs two NICs - one for source machine connectivity, and one for Azure failback connectivity. In questo modo si evitala sovrapposizione degli intervalli di indirizzi di subnet per l'origine e il failover delle macchine virtuali.
**Server di elaborazione in AzureProcess server in Azure** | È necessario un server di elaborazione in Azure prima di poter eseguire il failback al sito locale.<br/><br/> Il server di elaborazione riceve i dati dalla macchina virtuale di Azure protetta e li invia al sito locale.<br/><br/> È necessaria una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta, pertanto è consigliabile distribuire il server di elaborazione in Azure per ottenere prestazioni di replica più elevate.<br/><br/> Per il proof-of-concept, è possibile usare il server di elaborazione locale ed ExpressRoute con peering privato.<br/><br/> Il server di elaborazione deve trovarsi nella rete di Azure in cui si trova la macchina virtuale di cui è stato eseguito il failover. Il server di elaborazione deve inoltre essere in grado di comunicare con il server di configurazione locale e il server di destinazione master.
**Server di destinazione master separato** | Il server di destinazione master riceve i dati di failback e per impostazione predefinita un server di destinazione master di Windows viene eseguito nel server di configurazione locale.<br/><br/> A un server di destinazione master possono essere collegati fino a 60 dischi. Il failback delle macchine virtuali con più di un totale complessivo di 60 dischi oppure, se si esegue il failback di grandi volumi di traffico, creare un server di destinazione master separato per il failback.<br/><br/> Se le macchine vengono raccolte in un gruppo di replica per la coerenza di più macchine virtuali, le macchine virtuali devono essere tutte Windows o tutte Linux.If machines are gathered into a replication group for multi-VM consistency, the VMs must all be Windows, or must all be Linux. Perché? Poiché tutte le macchine virtuali in un gruppo di replica devono utilizzare lo stesso server di destinazione master e il server di destinazione master deve avere lo stesso sistema operativo (con la stessa versione o una versione successiva) di quelle dei computer replicati.<br/><br/> Il server di destinazione master non deve avere snapshot nei propri dischi, altrimenti la riprotezione e il failback non funzioneranno.<br/><br/> Il server di destinazione master non può avere un controller SCSI Paravirtual. Il controller può essere solo un controller LSI Logic. Senza un controller LSI Logic, la riprotezione non riuscirà.
**Criteri di replica di failback** | Per eseguire la replica nel sito locale, è necessario un criterio di failback. Questo criterio viene creato automaticamente quando si crea un criterio di replica in Azure.This policy is automatically created when you create a replication policy to Azure.<br/><br/> I criteri vengono automaticamente associati al server di configurazione. È impostata su una soglia RPO di 15 minuti, una conservazione del punto di ripristino di 24 ore e una frequenza di snapshot coerente con l'app è di 60 minuti. Il criterio non può essere modificato. 
**Peering privato da sito a sito VPN/ExpressRoute** | La riprotezione e il failback necessitano di una connessione VPN da sito a sito o di peering privato ExpressRoute per replicare i dati. 


## <a name="ports-for-reprotectionfailback"></a>Porte per la riprotezione/failback

È necessario che alcune porte siano aperte per la riprotezione/failback. Nella figura seguente vengono illustrate le porte e il flusso di riprotezione/failback.

![Porte per il failover e il failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Distribuire un server di elaborazione in Azure

1. [Configurare un server di elaborazione](vmware-azure-set-up-process-server-azure.md) in Azure per il failback.
2. Verificare che le macchine virtuali di Azure possano raggiungere il server di elaborazione. 
3. Assicurarsi che la connessione VPN da sito a sito o la rete di peering privato ExpressRoute disponga di larghezza di banda sufficiente per inviare dati dal server di elaborazione al sito locale.

## <a name="deploy-a-separate-master-target-server"></a>Distribuire un server di destinazione master distinto

1. Prendere nota dei requisiti e delle [limitazioni](#reprotectionfailback-components)del server di destinazione master .
2. Creare un server di destinazione master [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) o [Linux](vmware-azure-install-linux-master-target.md) in modo che corrisponda al sistema operativo delle macchine virtuali che si desidera riproteggere ed eseguire il failback.
3. Assicurarsi di non utilizzare vMotion di archiviazione per il server di destinazione master o che il failback abbia esito negativo. Impossibile avviare il computer della macchina virtuale perché i dischi non sono disponibili.
    - Per evitare questo problema, escludere il server di destinazione master dall'elenco vMotion.
    - Se una destinazione master viene sottoposta a un'attività vMotion di archiviazione dopo la riprotezione, i dischi della macchina virtuale protetti collegati al server di destinazione master vengono migrati alla destinazione dell'attività vMotion. Se si tenta di eseguire il failback dopo questo, disconnessione del disco non riesce perché i dischi non vengono trovati. È quindi difficile trovare i dischi negli account di archiviazione. In questo caso, individuarli manualmente e collegarli alla macchina virtuale. Successivamente, è possibile avviare la macchina virtuale locale.

4. Aggiungere un'unità di conservazione al server di destinazione master di Windows esistente. Aggiungere un nuovo disco e formattare l'unità. L'unità di conservazione viene usata per arrestare i punti nel tempo in cui la macchina virtuale viene replicata nel sito locale. Tenere presente questi criteri. Se non vengono soddisfatti, l'unità non è elencata per il server di destinazione master:
    - Il volume non viene utilizzato per altri scopi, ad esempio una destinazione di replica, e non è in modalità di blocco.
    - Il volume non è un volume della cache. Il volume di installazione personalizzata per il server di elaborazione e il server di destinazione master non è idoneo per un volume di conservazione. Quando il server di elaborazione e il server di destinazione master vengono installati in un volume, si tratta di un volume della cache del server di destinazione master.
    - Il file system del volume non è di tipo FAT o FAT32.
    - La capacità del volume è diversa da zero.
    - Il volume di conservazione predefinito per Windows è il volume R.
    - Il volume di conservazione predefinito per Linux è /mnt/retention/.

5. Aggiungere un'unità se si utilizza un server di elaborazione esistente. La nuova unità deve soddisfare i requisiti dell'ultimo passaggio. Se l'unità di conservazione non è presente, non verrà inclusa nell'elenco a discesa di selezione nel portale. Dopo aver aggiunto un'unità alla destinazione master locale, sono necessari fino a 15 minuti perché l'unità appaia nell'elenco di selezione nel portale. È possibile aggiornare il server di configurazione se l'unità non viene visualizzata dopo 15 minuti.
6. Installare gli strumenti VMware oppure open-vm-tools nel server master di destinazione. Senza gli strumenti, non è possibile rilevare gli archivi dati nell'host ESXi del server di destinazione master.
7. Impostare il disco. L'impostazione EnableUUID: true nei parametri di configurazione della macchina virtuale di destinazione master in VMware. Se questa riga non esiste, aggiungerla. Questa impostazione è necessaria per fornire un valore UUID coerente al file VMDK in modo che venga montato correttamente.
8. Verificare i requisiti di accesso di vCenter Server:
    - Se la macchina virtuale in cui si esegue il failback si trova in un host ESXi gestito da VMware vCenter Server, il server di destinazione master deve accedere al file di disco macchina virtuale (VMDK) della macchina virtuale locale per scrivere i dati replicati nei dischi della macchina virtuale. Assicurarsi che l'archivio dati della macchina virtuale locale sia montato nell'host di destinazione master con accesso in lettura/scrittura.
    - Se la macchina virtuale non si fa parte di un host ESXi gestito da un server VMware vCenter, Site Recovery crea una nuova macchina virtuale durante la riprotezione. Questa macchina virtuale viene creata nell'host ESXi in cui si crea la macchina virtuale del server di destinazione master. Scegliere con attenzione l'host ESXi per creare la macchina virtuale nell'host desiderato. Il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile dall'host in cui è in esecuzione il server di destinazione master.
    - Un'altra opzione, in presenza della VM locale per il failback, consiste nell'eliminarla prima di eseguire un failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESXi di destinazione master. Quando si esegue il failback in una posizione alternativa, i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESXi utilizzato dal server di destinazione master locale.
9. Per i computer fisici che eseguono il failback alle macchine virtuali VMware, è necessario completare l'individuazione dell'host in cui è in esecuzione il server di destinazione master, prima di poter proteggere nuovamente il computer.
10. Verificare che all'host ESXi a cui sia collegato la macchina virtuale di destinazione del server master sia collegato almeno un archivio dati DEL file system della macchina virtuale (VMFS). Se non sono associati archivi dati VMFS, l'input dell'archivio dati nelle impostazioni di riprotezione è vuoto e non è possibile procedere.


## <a name="next-steps"></a>Passaggi successivi

[Proteggere nuovamente](vmware-azure-reprotect.md) una macchina virtuale.
