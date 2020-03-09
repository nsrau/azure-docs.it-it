---
title: Domande frequenti sulla migrazione di Azure Migrate server
description: Risposte alle domande comuni sull'uso della migrazione del server Azure Migrate per eseguire la migrazione dei computer.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927524"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrazione di Azure Migrate server: domande comuni

Questo articolo risponde alle domande più comuni sull'Azure Migrate: strumento di migrazione server. Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande sull' [appliance Azure migrate](common-questions-appliance.md)
- Domande su [individuazione, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Come funziona la replica VMware senza agenti?

Il metodo di replica senza agenti per VMware USA snapshot VMware e il rilevamento dei blocchi modificati VMware (CBT).

Il processo è il seguente:

1. Quando si avvia la replica, viene pianificato un ciclo di replica iniziale. Nel ciclo iniziale viene effettuato uno snapshot della macchina virtuale. Lo snapshot viene usato per replicare le macchine virtuali VMDK (dischi). 
2. Al termine del ciclo di replica iniziale, delta replication cicli vengono pianificati periodicamente.
    - Durante delta replication viene utilizzato uno snapshot e i blocchi di dati che sono stati modificati dopo il ciclo di replica precedente vengono replicati.
    - VMware CBT viene usato per determinare i blocchi che sono stati modificati dopo l'ultimo ciclo.
    - La frequenza dei cicli di replica periodici viene gestita automaticamente da Azure Migrate e dipende dal numero di macchine virtuali e dischi simultaneamente replicati dallo stesso archivio dati. In condizioni ideali, la replica converge a un ciclo all'ora per ogni macchina virtuale.

Quando si esegue la migrazione, viene pianificato un ciclo di replica su richiesta per il computer per l'acquisizione dei dati rimanenti. Per evitare la perdita di dati e la coerenza dell'applicazione, è possibile scegliere di arrestare il computer durante la migrazione.

## <a name="why-isnt-resynchronization-exposed"></a>Perché non viene esposta la risincronizzazione?

Durante la migrazione senza agenti, in ogni ciclo Delta viene scritta la differenza tra lo snapshot corrente e quello creato in precedenza. È sempre la differenza tra gli snapshot e i dati di riduzione. Se un settore specifico viene scritto *N* volte tra gli snapshot, solo l'ultima scrittura deve essere trasferita perché si è interessati solo all'ultima sincronizzazione. Il processo è diverso dalla replica basata su agenti, durante il quale si tiene traccia e si applica ogni operazione di scrittura. In questo processo ogni ciclo Delta è una risincronizzazione. Non viene quindi esposta alcuna opzione di risincronizzazione. Se i dischi non sono mai sincronizzati a causa di un errore, vengono corretti nel ciclo successivo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>In che modo la percentuale di varianza influisce sulla replica senza agenti?

Poiché la replica senza agenti si riduce nei dati, il *modello di varianza* è più importante della frequenza di *varianza*. Quando un file viene scritto di nuovo e anche in questo caso, la frequenza non ha un notevole effetto. Tuttavia, un modello in cui viene scritto ogni altro settore causa una varianza elevata nel ciclo successivo. Poiché si riduce al minimo la quantità di dati trasferiti, è possibile ridurre il più possibile i dati prima di pianificare il ciclo successivo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Con quale frequenza viene pianificato un ciclo di replica?

La formula per pianificare il ciclo di replica successivo è (tempo ciclo precedente/2) o un'ora, a seconda del valore maggiore.

Se, ad esempio, una macchina virtuale impiega quattro ore per un ciclo Delta, il ciclo successivo viene pianificato in due ore e non nell'ora successiva. Il processo è diverso immediatamente dopo la replica iniziale, quando il primo ciclo Delta viene pianificato immediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>In che modo la replica senza agenti influisce sui server VMware?

La replica senza agenti produce un certo effetto sulle prestazioni per gli host server VMware vCenter e VMware ESXi. Poiché la replica senza agenti USA snapshot, USA IOPS sull'archiviazione, quindi è necessaria una larghezza di banda di archiviazione di IOPS. Non è consigliabile usare la replica senza agenti se sono presenti vincoli sull'archiviazione o IOPs nell'ambiente in uso.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>È possibile eseguire la migrazione senza agenti di VM UEFI ad Azure gen 2?

No. Usare Azure Site Recovery per eseguire la migrazione di queste macchine virtuali alle macchine virtuali di Azure di seconda generazione. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>È possibile aggiungere macchine virtuali a zone di disponibilità di Azure durante la migrazione?

No. Zone di disponibilità di Azure non sono supportate per la migrazione del Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Quale protocollo di trasporto Azure Migrate utilizzare durante la replica?

Azure Migrate usa il protocollo NBD (Network Block Device) con la crittografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual è la versione minima di server vCenter necessaria per la migrazione?

È necessario avere almeno server vCenter 5,5 e vSphere ESXi host versione 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>I clienti possono migrare le proprie macchine virtuali a dischi non gestiti?

No. Azure Migrate supporta solo la migrazione a Managed Disks (HDD Standard, SSD Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quante VM è possibile replicare contemporaneamente usando la migrazione senza agenti?

Attualmente, è possibile eseguire la migrazione di VM 100 per ogni istanza di server vCenter simultaneamente. Eseguire la migrazione in batch di 10 macchine virtuali.
 
## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
