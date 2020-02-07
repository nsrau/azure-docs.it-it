---
title: Domande frequenti sulla migrazione di Azure Migrate server
description: Risposte alle domande comuni sulla migrazione di Azure Migrate server
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067383"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrazione di Azure Migrate server: domande comuni

Questo articolo risponde alle domande più comuni sull'Azure Migrate: migrazione del server. Se si hanno altre query dopo aver letto questo articolo, pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum). Per altre domande, vedere gli articoli seguenti:

- [Domande generali](resources-faq.md) su Azure migrate.
- [Domande](common-questions-appliance.md) sull'appliance Azure migrate.
- [Domande](common-questions-discovery-assessment.md) sull'individuazione, la valutazione e la visualizzazione delle dipendenze.


## <a name="how-does-agentless-vmware-replication-work"></a>Come funziona la replica VMware senza agenti?

Il metodo di replica senza agenti per VMware utilizza gli snapshot VMware e il rilevamento dei blocchi modificato (CBT) di VMware. Un ciclo di replica iniziale viene pianificato quando l'utente avvia la replica. Nel ciclo di replica iniziale viene usato uno snapshot della macchina virtuale e questo snapshot viene usato per replicare le VM VMDK (dischi). Al termine del ciclo di replica iniziale, delta replication cicli vengono pianificati periodicamente. Nel ciclo delta replication viene utilizzato uno snapshot e i blocchi di dati che sono stati modificati dopo la replica del ciclo di replica precedente. VMware Changed Block Tracking viene usato per determinare i blocchi che sono stati modificati dopo l'ultimo ciclo.
La frequenza dei cicli di replica periodica viene gestita automaticamente dal servizio, a seconda del numero di macchine virtuali/dischi che eseguono contemporaneamente la replica nello stesso archivio dati e in condizioni ideali la convergenza a 1 ciclo all'ora per ogni macchina virtuale.

Quando si esegue la migrazione, viene pianificato un ciclo di replica su richiesta per la macchina virtuale per l'acquisizione dei dati rimanenti. È possibile scegliere di arrestare la VM come parte della migrazione per evitare la perdita di dati e la coerenza delle applicazioni.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Perché l'opzione di risincronizzazione non è esposta nello stack senza agenti?

Nello stack senza agenti, in ogni ciclo Delta, viene trasferita la differenza tra lo snapshot corrente e quello precedente. Poiché si tratta sempre di una differenza tra gli snapshot, questo offre il vantaggio di ripiegare i dati (ad esempio, se un particolare settore è scritto ' n'volte tra gli snapshot, è sufficiente trasferire l'ultima scrittura, perché si è interessati solo all'ultima sincronizzazione). Questa operazione è diversa dallo stack basato su agenti, in cui viene tenuta traccia di ogni scrittura e di applicazione. Ciò significa che ogni ciclo Delta è una risincronizzazione. Non viene pertanto esposta alcuna opzione di risincronizzazione. 

Se mai i dischi non sono sincronizzati a causa di un errore, vengono corretti nel ciclo successivo. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Qual è l'effetto della frequenza di varianza se si utilizza la replica senza agenti?

Poiché lo stack dipende dai dati che vengono ridotti, oltre alla frequenza di varianza, il modello di varianza è quello che conta in questo stack. Modello in cui un file viene scritto nuovamente e non ha un effetto significativo. Tuttavia, un modello in cui viene scritto ogni altro settore provocherà una varianza elevata nel ciclo successivo. Poiché si riduce al minimo la quantità di dati trasferiti, è possibile ridurre il più possibile i dati prima di pianificare il ciclo successivo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Con quale frequenza viene pianificato un ciclo di replica?

La formula per pianificare il ciclo di replica successivo è la seguente: (tempo ciclo precedente/2) o 1 ora a seconda del valore maggiore. Se, ad esempio, una macchina virtuale ha impiegato quattro ore per un ciclo Delta, il ciclo successivo verrà pianificato in 2 ore e non nell'ora successiva. Questa operazione è diversa quando il ciclo si trova immediatamente dopo il runtime di integrazione, dove si pianifica immediatamente il primo ciclo Delta.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Qual è l'effetto sulle prestazioni dell'host server vCenter o ESXi quando si usa la replica senza agenti?

Poiché la replica senza agenti USA snapshot, il consumo di operazioni di i/o al secondo per le operazioni di i/o al secondo, per i clienti sarà necessaria una capacità di IOPs. Non è consigliabile usare questo stack nell'ambiente vincolato di archiviazione/IOPs.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Lo stack di migrazione senza agenti supporta la migrazione di VM UEFI in macchine virtuali di Azure gen 2?

No, è necessario usare Azure Site Recovery per eseguire la migrazione di queste macchine virtuali alle macchine virtuali di Azure di seconda generazione. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>È possibile aggiungere le macchine virtuali a zone di disponibilità di Azure durante la migrazione?

No, il supporto per zone di disponibilità di Azure non è presente.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Quale protocollo di trasporto viene utilizzato da Azure Migrate durante la replica?

Azure Migrate usa il protocollo NBD (Network Block Device) con la crittografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual è la versione minima di server vCenter necessaria per la migrazione?

È necessario avere almeno server vCenter 5,5 e VMware vSphere host ESXi versione 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>I clienti possono migrare le proprie macchine virtuali a dischi non gestiti?

No. Azure Migrate supporta solo la migrazione a Managed Disks (HDD standard, unità SSD Premium).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Quante VM possono essere replicate contemporaneamente usando lo stack VMware senza agenti?

Attualmente, i clienti possono eseguire la migrazione di 100 VM per ogni server vCenter simultaneamente. Questa operazione può essere eseguita in batch di 10 macchine virtuali.




