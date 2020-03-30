---
title: Azure Migrate Server Migration FAQ
description: Risposte alle domande comuni sull'uso della migrazione del server di azure per eseguire la migrazione dei computer.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 507cc8088bf54b1a4f4483673ec5332efcdd36c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127809"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrazione server di azure di migrazione: domande comuniAzure Migrate Server Migration: Common questions

Questo articolo risponde alle domande comuni sullo strumento Migrazione server di Azure.This article answers common questions about the Azure Migrate: Server Migration tool. Se hai altre domande, controlla queste risorse:

- [Domande generali](resources-faq.md) su Azure Migrate
- Domande [sull'appliance Azure Migrate](common-questions-appliance.md)
- Domande su [scoperta, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Ottenere risposte alle domande nel forum di [Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Come funziona la replica VMware senza agente?

Il metodo di replica senza agente per VMware utilizza gli snapshot di VMware e VMware Changed Block Tracking (CBT).

Il processo è il seguente:

1. Quando si avvia la replica, viene pianificato un ciclo di replica iniziale. Nel ciclo iniziale viene creato uno snapshot della macchina virtuale. Lo snapshot viene usato per replicare le Macchine virtuali VMDK (dischi). 
2. Al termine del ciclo di replica iniziale, i cicli di replica differenziale vengono pianificati periodicamente.
    - Durante la replica delta, viene creato uno snapshot e vengono replicati blocchi di dati che sono stati modificati rispetto al ciclo di replica precedente.
    - VMware CBT viene utilizzato per determinare i blocchi che sono stati modificati dall'ultimo ciclo.
    - La frequenza dei cicli di replica periodici viene gestita automaticamente da Azure Migrate e dipende dal numero di altre macchine virtuali e dischi che eseguono contemporaneamente la replica dallo stesso archivio dati. In condizioni ideali, la replica converge infine a un ciclo all'ora per ogni macchina virtuale.

Quando si esegue la migrazione, viene pianificato un ciclo di replica su richiesta per il computer per acquisire i dati rimanenti. Per garantire zero perdita di dati e la coerenza dell'applicazione, è possibile scegliere di arrestare il computer durante la migrazione.

## <a name="why-isnt-resynchronization-exposed"></a>Perché non viene esposta la risincronizzazione?

Durante la migrazione senza agente, in ogni ciclo delta viene scritta la differenza tra lo snapshot corrente e lo snapshot creato in precedenza. È sempre la differenza tra istantanee, ripiegamento dei dati. Se un settore specifico viene scritto *N* volte tra gli snapshot, solo l'ultima scrittura deve essere trasferita perché siamo interessati solo all'ultima sincronizzazione. Il processo è diverso dalla replica basata su agente, durante la quale viene tenuta traccia e applicata ogni scrittura. In questo processo, ogni ciclo delta è una risincronizzazione. Pertanto, nessuna opzione di risincronizzazione esposta. Se i dischi non sono mai sincronizzati a causa di un errore, viene risolto nel ciclo successivo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>In che modo la frequenza di varianza influisce sulla replica senza agente?

Poiché la replica senza agente si ripiega nei dati, il modello di *varianza* è più importante del tasso di *varianza*. Quando un file viene scritto più e più volte, la frequenza non ha molto impatto. Tuttavia, un modello in cui ogni altro settore è scritto provoca un'elevata varianza nel ciclo successivo. Poiché riduciamo al minimo la quantità di dati che trasferiamo, permettiamo ai dati di piegarsi il più possibile prima di pianificare il ciclo successivo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Con quale frequenza viene pianificato un ciclo di replica?

La formula per pianificare il ciclo di replica successivo è (tempo ciclo precedente / 2) o un'ora, a seconda di quale sia superiore.

Ad esempio, se una macchina virtuale richiede quattro ore per un ciclo delta, il ciclo successivo viene pianificato in due ore e non nell'ora successiva. Il processo è diverso immediatamente dopo la replica iniziale, quando il primo ciclo delta viene pianificato immediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>In che modo la replica senza agente influisce sui server VMware?

La replica senza agente comporta un impatto sulle prestazioni degli host VMware vCenter Server e VMware ESXi. Poiché la replica senza agente utilizza snapshot, utilizza operazioni di I/O al secondo nell'archiviazione, pertanto è necessaria una larghezza di banda di archiviazione per le operazioni di I/O al secondo. Non è consigliabile usare la replica senza agente se sono previsti vincoli per l'archiviazione o le informazioni di I/O al secondo nell'ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>È possibile eseguire la migrazione senza agente di macchine virtuali UEFI in Azure Gen 2?

No. Usare Azure Site Recovery per eseguire la migrazione di queste macchine virtuali alle macchine virtuali di Azure di generazione 2.Use Azure Site Recovery to migrate these VMs to Gen 2 Azure VMs. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>È possibile aggiungere le macchine virtuali alle zone di disponibilità di Azure durante la migrazione?

No. Le zone di disponibilità di Azure non sono supportate per la migrazione di Azure Migrate.Azure Availability zones aren't supported for Azure Migrate migration.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Quale protocollo di trasporto usa Azure Migrate durante la replica?

Azure Migrate usa il protocollo NBD (Network Block Device) con crittografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual è la versione minima di vCenter Server necessaria per la migrazione?

È necessario disporre almeno dell'host vCenter Server 5.5 e dell'host vSphere ESXi versione 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>I clienti possono eseguire la migrazione delle macchine virtuali a dischi non gestiti?

No. Azure Migrate supporta la migrazione solo ai dischi gestiti (HDD standard, SSD Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quante macchine virtuali è possibile replicare contemporaneamente usando la migrazione senza agente?

Attualmente, è possibile eseguire contemporaneamente la migrazione di 100 macchine virtuali per istanza di vCenter Server. Eseguire la migrazione in batch di 10 macchine virtuali.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Come è possibile limitare la replica nell'uso dell'appliance di Azure Migrate per la replica VMware senza agente?  

È possibile limitare l'utilizzo di NetQosPolicy.You can throttle using NetQosPolicy. Ad esempio:

Il AppNamePrefix da utilizzare in NetQosPolicy è "GatewayWindowsService.exe". È possibile creare criteri nell'appliance di Azure Migrate per limitare il traffico di replica dall'appliance creando criteri come questo:You could create a policy on the Azure Migrate appliance to throttle replication traffic from the appliance by creating a policy such a policy such a:
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Quando si esegue la migrazione dei computer come server fisici?

La migrazione di computer come se fossero server fisici è utile in diversi scenari:

- Quando si esegue la migrazione di server fisici locali.
- Se si esegue la migrazione di macchine virtuali virtualizzate da piattaforme come Xen, KVM.
- Per eseguire la migrazione delle macchine virtuali Hyper-V o VMware, se per qualche motivo non è possibile utilizzare il processo di migrazione standard per la migrazione di [Hyper-V](tutorial-migrate-hyper-v.md)o [VMware.](server-migrate-overview.md) Ad esempio, se non si esegue VMware vCenter e si utilizzano solo host ESXi.
- To migrate VMs that are currently running in private clouds to Azure
- Se si desidera eseguire la migrazione di macchine virtuali in esecuzione in cloud pubblici come Amazon Web Services (AWS) o Google Cloud Platform (GCP) in Azure.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>È necessario VMware vCenter per eseguire la migrazione delle macchine virtuali VMware?
Per [eseguire la migrazione delle macchine virtuali VMware](server-migrate-overview.md) tramite la migrazione basata su agenti o senza agente Di VMware, gli host ESXi in cui si trovano le macchine virtuali devono essere gestiti da vCenter Server. Se non si dispone di vCenter Server, è possibile eseguire la migrazione delle macchine virtuali VMware eseguendo ne esegue la migrazione come server fisici. [Scopri di più](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Passaggi successivi

Leggere la [panoramica di Azure Migrate](migrate-services-overview.md).
