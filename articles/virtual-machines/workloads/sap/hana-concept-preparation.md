---
title: Principi del ripristino di emergenza e preparazione a SAP HANA di Azure (istanze Large) | Microsoft Docs
description: Principi del ripristino di emergenza e preparazione a SAP HANA di Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb1ed063cb11a82d786badd3f63b2d4b6932ce13
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709721"
---
# <a name="disaster-recovery-principles"></a>Principi relativi al ripristino di emergenza

HANA in istanze Large offre una funzionalità di ripristino di emergenza tra gli indicatori di HANA in istanze Large in diverse aree di Azure. Ad esempio, se si distribuiscono unità HANA in istanze Large nell'area Stati Uniti occidentali di Azure, è possibile usare unità HANA in istanze Large nell'area Stati Uniti orientali come unità di ripristino di emergenza. Come accennato in precedenza, il ripristino di emergenza non viene configurato automaticamente poiché richiede il pagamento di un'altra unità di istanze Large di HANA nell'area di ripristino di emergenza. La configurazione di ripristino di emergenza funziona per le configurazioni con scalabilità verticale e con scalabilità orizzontale. 

Negli scenari distribuiti fino ad ora, i clienti hanno usato l'unità nell'area di ripristino di emergenza per eseguire sistemi non di produzione che usano un'istanza di HANA installata. Lo SKU dell'unità di istanze Large di HANA deve corrispondere a quello usato per scopi di produzione. L'immagine seguente mostra l'aspetto che avrà la configurazione dei dischi tra l'unità server nell'area di produzione di Azure e l'area di ripristino di emergenza:

![Configurazione di ripristino di emergenza dal punto di vista dei dischi](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Come illustrato in questo grafico di sintesi, è necessario ordinare un secondo set di volumi di dischi. I volumi di dischi di destinazione hanno le stesse dimensioni dei volumi di produzione per l'istanza di produzione nell'unità di ripristino di emergenza. I volumi di dischi, inoltre, sono associati all'unità server di istanze Large di HANA nel sito di ripristino di emergenza. Dall'area di produzione al sito di ripristino di emergenza vengono replicati i volumi seguenti:

- /hana/data
- /hana/logbackups 
- /hana/shared (include /usr/sap)

Il volume /hana/log non viene replicato perché il log delle transazioni SAP HANA non è necessario nella modalità con cui viene eseguito il ripristino da questi volumi. 

Alla base della funzionalità di ripristino di emergenza offerta vi è la funzionalità di replica dell'archiviazione fornita dall'infrastruttura HANA in istanze Large. La funzionalità usata per l'archiviazione non è costituita da un flusso costante di modifiche replicate in modo asincrono man mano che vengono apportate al volume di archiviazione. Si tratta invece di un meccanismo basato sul fatto che gli snapshot di questi volumi vengono creati a intervalli regolari. Il differenziale tra uno snapshot già replicato e un nuovo snapshot non ancora replicato viene quindi trasferito nel sito di ripristino di emergenza in volumi di dischi di destinazione.  Questi snapshot vengono archiviati nei volumi e, in caso di failover nel ripristino di emergenza, devono essere ripristinati nei volumi.  

Il primo trasferimento dei dati completi del volume deve essere eseguito prima che la quantità di dati sia inferiore ai differenziali tra gli snapshot. I volumi nel sito di ripristino di emergenza contengono quindi tutti gli snapshot dei volumi eseguiti nel sito di produzione. È infine possibile usare il sistema di ripristino di emergenza per ottenere uno stato precedente per il recupero dei dati persi, senza eseguire il rollback del sistema di produzione.

In caso di distribuzioni MCOD con più istanze indipendenti di SAP HANA su un'unità di istanza HANA Large, è previsto che per tutte le istanze di SAP HANA sia replicata l'archiviazione insieme al ripristino di emergenza.

Nei casi in cui si usi la replica del sistema HANA come funzionalità a disponibilità elevata nel sito di produzione e si usi la replica basata sull'archiviazione per il sito di ripristino di emergenza, vengono replicati i volumi di entrambi i nodi dal sito primario all'istanza di ripristino di emergenza. È necessario acquistare risorse di archiviazione aggiuntive (con la stessa dimensione del nodo primario) per il sito di ripristino di emergenza per consentire la replica di entrambi, primario e secondario, nel sito di ripristino di emergenza. 



>[!NOTE]
>La funzionalità di replica dell'archiviazione di HANA in istanze Large esegue il mirroring e la replica degli snapshot di archiviazione. Se non si eseguono snapshot di archiviazione come descritto nella sezione Backup e ripristino di questo articolo, non è possibile eseguire alcuna replica nel sito di ripristino di emergenza. L'esecuzione di snapshot di archiviazione è un prerequisito per la replica di archiviazione nel sito di ripristino di emergenza.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparazione dello scenario di ripristino di emergenza
In questo scenario è presente un sistema di produzione in esecuzione in HANA in istanze Large nell'area di Azure di produzione. Per i passaggi che seguono, si presupponga che il SID del sistema HANA è "PRD" e che è presente un sistema non di produzione in esecuzione in HANA in istanze Large nell'area di Azure di ripristino di emergenza. Nel secondo caso, si supponga che il SID è "TST". L'immagine seguente mostra questa configurazione:

![Avvio della configurazione di ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se l'istanza del server non è già stata ordinata con il set di volumi di archiviazione aggiuntivo, SAP HANA in Azure Service Management collega il set di volumi aggiuntivo come destinazione per la replica di produzione all'unità HANA in istanze Large in cui viene eseguita l'istanza HANA TST. A questo scopo, è necessario fornire il SID dell'istanza di produzione HANA. Quando il servizio SAP HANA in Azure Service Management avrà confermato l'associazione di questi volumi, sarà necessario montare i volumi nell'unità di istanze Large di HANA.

![Passaggio successivo della configurazione di ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Il passaggio successivo consiste nell'installare la seconda istanza di SAP HANA nell'unità HANA in istanze Large nell'area di Azure di ripristino di emergenza in cui viene eseguita l'istanza HANA TST. L'istanza di SAP HANA appena installata deve avere lo stesso SID e gli utenti creati devono avere lo stesso UID e ID gruppo dell'istanza di produzione. Leggere [Backup e ripristino](hana-backup-restore.md) per i dettagli. Se l'installazione ha esito positivo, è necessario:

- Eseguire il passaggio 2 della preparazione dello snapshot di archiviazione descritta in [Backup e ripristino](hana-backup-restore.md).
- Se non è ancora stato fatto, creare una chiave pubblica per l'unità HANA in istanze Large. Vedere il passaggio 3 della preparazione dello snapshot di archiviazione descritta in [Backup e ripristino](hana-backup-restore.md).
- Mantenere il *HANABackupCustomerDetails.txt* con la nuova istanza HANA ed eseguire il test che verifichi che la connettività nell'account di archiviazione funzioni correttamente.  
- Arrestare l'istanza di SAP HANA appena installata nell'unità HANA in istanze Large nell'area di Azure di ripristino di emergenza.
- Smontare i volumi PRD e contattare il servizio SAP HANA in Azure Service Management. I volumi non possono rimanere montati nell'unità poiché non devono risultare accessibili durante il funzionamento come destinazione della replica di archiviazione.  

![Passaggio della configurazione di ripristino di emergenza prima di definire la replica](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Il team operativo stabilisce la relazione di replica tra i volumi PRD nell'area di Azure di produzione e i volumi PRD nell'area di Azure di ripristino di emergenza.

>[!IMPORTANT]
>Il volume /hana/log non viene replicato perché non è necessario ripristinare nel sito di ripristino di emergenza il database SAP HANA replicato in uno stato coerente.

Successivamente, configurare o regolare la pianificazione del backup dello snapshot di archiviazione per conseguire gli obiettivi RTO e RPO definiti in caso di emergenza. Per ridurre al minimo l'obiettivo del punto di ripristino (RPO), impostare gli intervalli di replica seguenti nel servizio di istanze Large di HANA:
- Per i volumi gestiti dallo snapshot combinato (tipo di snapshot **HANA**), impostare la replica ogni 15 minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.
- Per il volume di backup del log delle transazioni (tipo di snapshot **log**) impostare la replica ogni tre minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.

Per ridurre al minimo l'obiettivo del punto di ripristino, impostare quanto segue:
- Eseguire uno snapshot di archiviazione di tipo **hana** (vedere "Passaggio 7: eseguire gli snapshot") ogni 30 minuti fino a 1 ora.
- Eseguire i backup del log delle transazioni SAP HANA ogni cinque minuti.
- Eseguire uno snapshot di archiviazione di tipo **logs** ogni 5-15 minuti. Con questo intervallo, dovrebbe essere possibile ottenere un valore RPO di circa 15-25 minuti.

Con questa configurazione, la sequenza dei backup del log delle transazioni, degli snapshot di archiviazione e dalla replica del volume di backup del log delle transazioni HANA e dei volumi /hana/data e /hana/shared (che include /usr/sap) dovrebbe essere simile ai dati mostrati in questo grafico:

 ![Relazione tra uno snapshot dei backup del log delle transazioni e uno snapmirror su un asse temporale](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Per ottenere un valore RPO migliore in caso di ripristino di emergenza, è possibile copiare i backup del log delle transazioni HANA da SAP HANA in Azure (istanze Large) all'altra area di Azure. Per ottenere questa ulteriore riduzione del valore RPO, completare questi passaggi:

1. Eseguire il backup del log delle transazioni di HANA il più frequentemente possibile in /hana/logbackups.
1. Usare rsync per copiare i backup del log delle transazioni nelle macchine virtuali di Azure ospitate nella condivisione NFS, che si trovano in reti virtuali di Azure nell'area di produzione di Azure e nelle aree di ripristino di emergenza. È necessario connettere entrambe le reti virtuali di Azure al circuito che stabilisce la connessione tra le istanze Large di HANA di produzione e Azure. Vedere il grafico disponibile nella sezione [Considerazioni sulla rete per il ripristino di emergenza con istanze Large di HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances). 
1. Mantenere i backup del log delle transazioni nell'area nella macchina virtuale collegata all'archiviazione NFS esportata.
1. In caso di failover di emergenza, integrare i backup del log delle transazioni disponibili nel volume /hana/logbackups con i backup del log delle transazioni acquisiti più di recente nella condivisione NFS nel sito di ripristino di emergenza. 
1. Avviare il ripristino del backup del log delle transazioni in base al backup più recente che è stato possibile salvare nell'area di ripristino di emergenza.

Quando le operazioni di HANA in istanze Large confermano la configurazione della relazione di replica e si avvia l'esecuzione dei backup degli snapshot di archiviazione, viene avviata anche la replica dei dati.

![Passaggio della configurazione di ripristino di emergenza prima di definire la replica](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Durante la replica, gli snapshot nei volumi PRD presenti nelle aree di Azure di ripristino di emergenza non vengono replicati, ma vengono solo archiviati. Se i volumi vengono montati con questo stato, rappresentano lo stato in cui sono stati smontati dopo l'installazione dell'istanza PRD di SAP HANA nell'unità server presente nell'area di Azure di ripristino di emergenza. Rappresentano anche i backup di archiviazione non ancora ripristinati.

In caso di failover, è possibile anche scegliere di ripristinare uno snapshot di archiviazione precedente anziché lo snapshot di archiviazione più recente.

## <a name="next-steps"></a>Passaggi successivi

- Consultare [Procedura di failover di ripristino di emergenza](hana-failover-procedure.md).
