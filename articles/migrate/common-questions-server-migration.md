---
title: Domande frequenti sulla migrazione di Azure Migrate server
description: Risposte alle domande comuni sulla migrazione dei computer con Azure Migrate migrazione del server
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425834"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrazione di Azure Migrate server: domande comuni

Questo articolo risponde alle domande più comuni sull'Azure Migrate: strumento di migrazione server. Per altre domande dopo aver letto questo articolo, vedere gli articoli seguenti:

- [Domande generali](resources-faq.md) su Azure migrate.
- [Domande](common-questions-appliance.md) sull'appliance Azure migrate.
- [Domande](common-questions-discovery-assessment.md) sull'individuazione, la valutazione e la visualizzazione delle dipendenze.
- Pubblicare domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Come funziona la replica VMware senza agenti?

Il metodo di replica senza agenti per VMware utilizza gli snapshot VMware e il rilevamento dei blocchi modificato (CBT) di VMware.

1. Quando si avvia la replica, viene pianificato un ciclo di replica iniziale. Nel ciclo iniziale viene effettuato uno snapshot della macchina virtuale. Questo snapshot viene usato per replicare le macchine virtuali VMDK (dischi). 
2. Al termine del ciclo di replica iniziale, delta replication cicli vengono pianificati periodicamente.
    - Durante delta replication viene utilizzato uno snapshot e i blocchi di dati che sono stati modificati dopo il ciclo di replica precedente vengono replicati.
    - VMware CBT viene usato per determinare i blocchi che sono stati modificati dopo l'ultimo ciclo.
    - La frequenza dei cicli di replica periodici viene gestita automaticamente dal Azure Migrate, a seconda del numero di macchine virtuali/dischi simultaneamente replicati dallo stesso archivio dati. In condizioni ideali, la replica converge a un ciclo all'ora per ogni macchina virtuale.

Quando si esegue la migrazione, viene pianificato un ciclo di replica su richiesta per il computer, in modo da acquisire i dati rimanenti. È possibile scegliere di arrestare il computer durante la migrazione, per evitare la perdita di dati e la coerenza delle applicazioni.

## <a name="why-isnt-resynchronization-exposed"></a>Perché non viene esposta la risincronizzazione?

Durante la migrazione senza agenti, in ogni ciclo Delta viene scritta la differenza tra lo snapshot corrente e quello creato in precedenza. Poiché è sempre la differenza tra gli snapshot, i dati vengono ripiegati in. Quindi, se un particolare settore viene scritto N volte tra gli snapshot, solo l'ultima scrittura deve essere trasferita, poiché si è interessati solo all'ultima sincronizzazione. Questo comportamento è diverso dalla replica basata su agenti, in cui si tiene traccia e si applica ogni operazione di scrittura. Ciò significa che ogni ciclo Delta è una risincronizzazione. Non viene pertanto esposta alcuna opzione di risincronizzazione. Se mai i dischi non sono sincronizzati a causa di un errore, vengono corretti nel ciclo successivo. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>In che modo la varianza ha un effetto sulla replica senza agenti?

Poiché la replica senza agenti riduce la data, il modello di varianza è più importante della varianza. Quando un file viene scritto di nuovo e anche in questo caso, la frequenza non ha un notevole effetto. Tuttavia, un modello in cui viene scritto ogni altro settore causa una varianza elevata nel ciclo successivo. Poiché si riduce al minimo la quantità di dati trasferiti, è possibile ridurre il più possibile i dati prima di pianificare il ciclo successivo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Con quale frequenza viene pianificato un ciclo di replica?

Formula per pianificare il ciclo di replica successivo: (tempo ciclo precedente/2) o 1 ora, a seconda del valore maggiore.

Se, ad esempio, una macchina virtuale impiega quattro ore per un ciclo Delta, il ciclo successivo viene pianificato in due ore e non nell'ora successiva. Questa operazione è diversa immediatamente dopo la replica iniziale, in cui il primo ciclo Delta è pianificato immediatamente.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>In che modo la replica senza agenti influisca sui server VMware?

Gli host server vCenter/ESXi hanno un certo effetto sulle prestazioni. Poiché la replica senza agenti USA snapshot, USA IOPs sull'archiviazione ed è necessaria una larghezza di banda di archiviazione di IOPS. Non è consigliabile usare la replica senza agenti se sono presenti vincoli sull'archiviazione/IOPs nell'ambiente in uso.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>È possibile eseguire la migrazione senza agenti di VM UEFI ad Azure gen 2?

No. Usare Azure Site Recovery per eseguire la migrazione di queste macchine virtuali alle macchine virtuali di Azure di seconda generazione. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>È possibile aggiungere macchine virtuali a zone di disponibilità di Azure durante la migrazione?

No, zone di disponibilità di Azure non sono supportate.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Quale protocollo di trasporto viene utilizzato da Azure Migrate durante la replica?

Azure Migrate usa il protocollo NBD (Network Block Device) con la crittografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual è la versione minima di server vCenter necessaria per la migrazione?

È necessario avere almeno server vCenter 5,5 e VMware vSphere host ESXi versione 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>I clienti possono migrare le proprie macchine virtuali a dischi non gestiti?

No. Azure Migrate supporta solo la migrazione a Managed Disks (HDD standard, unità SSD Premium).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Quante VM è possibile replicare insieme alla migrazione senza agenti?

Attualmente, è possibile eseguire la migrazione di VM 100 per ogni server vCenter simultaneamente. Eseguire la migrazione in batch di 10 macchine virtuali.
 



