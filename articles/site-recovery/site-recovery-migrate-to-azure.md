---
title: Eseguire la migrazione ad Azure con Site Recovery | Documentazione Microsoft
description: Questo articolo offre una panoramica della migrazione di VM e server fisici ad Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 20aecf6df4af0b89fb6cafbd2047facdb345bae5
ms.openlocfilehash: 8f658cb80a43e0eb21585ea851251b1339ab546b


---
# <a name="migrate-to-azure-with-site-recovery"></a>Eseguire la migrazione ad Azure con Site Recovery

Leggere questo articolo per una panoramica dell'uso del servizio Azure Site Recovery per la migrazione di macchine virtuali e server fisici.

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. La strategia di continuità aziendale e ripristino di emergenza deve garantire la sicurezza dei dati aziendali e la possibilità di recuperarli, oltre alla disponibilità costante dei carichi di lavoro in caso di emergenza.

Site Recovery è un servizio di Azure che contribuisce all'attuazione della strategia per la continuità aziendale e il ripristino di emergenza orchestrando la replica delle macchine virtuali e dei server fisici locali nel cloud di Azure o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

Questo articolo descrive la distribuzione nel [portale di Azure](https://portal.azure.com). Il [portale di Azure classico](https://manage.windowsazure.com/) può essere usato per gestire gli insiemi di credenziali di Site Recovery esistenti, ma non per crearne di nuovi.

È possibile inserire commenti nella parte inferiore di questo articolo. In caso di domande tecniche, visitare il [forum di Azure Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>Informazioni sulla migrazione

È possibile distribuire Site Recovery per la replica completa di server fisici e VM locali in Azure o in un sito secondario. Si esegue la replica dei computer, il relativo failover dal sito primario in caso di interruzioni e quindi il failback nel sito primario quando questo viene ripristinato. Oltre che per la replica completa, è possibile usare Site Recovery per eseguire la migrazione di VM e server fisici ad Azure e consentire così agli utenti di accedere al carico di lavoro del computer dalle VM di Azure. La migrazione comporta la replica e il failover dal sito primario ad Azure. A differenza della replica completa, tuttavia, non include un meccanismo di failback.

## <a name="what-can-site-recovery-migrate"></a>Elementi di cui è possibile eseguire la migrazione con Site Recovery

È possibile:

- Eseguire la migrazione di carichi di lavoro in esecuzione in server fisici e VM Hyper-V e VMware locali, perché vengano eseguiti in VM di Azure. In questo scenario è anche possibile eseguire la replica completa e il failback.
- Eseguire la migrazione di [VM IaaS di Azure](site-recovery-migrate-azure-to-azure.md) tra aree di Azure. Questo scenario supporta attualmente solo la migrazione, non il failback.
- Eseguire la migrazione di [istanze di Windows per Amazon Web Services](site-recovery-migrate-aws-to-azure.md) a VM IaaS di Azure. Questo scenario supporta attualmente solo la migrazione, non il failback.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Eseguire la migrazione di server fisici e VM locali

Per eseguire la migrazione di server fisici e VM VMware e Hyper-V locali, si segue approssimativamente la stessa procedura usata per la replica regolare. Si configurano un insieme di credenziali di Servizi di ripristino e quindi i server di gestione necessari (in base agli elementi di cui si vuole eseguire la migrazione), si aggiungono tali server all'insieme di credenziali e si specificano le impostazioni di replica. Dopo aver abilitato la replica per i computer di cui si vuole eseguire la migrazione, si effettua un rapido failover di test per verificare che tutto funzioni come previsto.

Dopo aver verificato il funzionamento dell'ambiente di replica, si usa un failover pianificato o non pianificato a seconda della [tipologia supportata](site-recovery-failover.md#failover-and-failback) per lo scenario. Per la migrazione, non è necessario eseguire il commit del failover o eliminare elementi. Si seleziona invece l'opzione **Completa la migrazione** per ogni computer di cui si vuole eseguire la migrazione. L'azione **Completa la migrazione** porta a termine il processo di migrazione, rimuove la replica per il computer e interrompe la relativa fatturazione di Site Recovery.

## <a name="migrate-between-azure-regions"></a>Eseguire la migrazione tra aree di Azure

È possibile eseguire la migrazione di VM di Azure tra aree usando Site Recovery. In questo scenario è supportata solo la migrazione. In altri termini, è possibile replicare VM di Azure ed effettuarne il failover in un'altra area, ma non eseguire il failback. In questo scenario si configura un insieme di credenziali di Servizi di ripristino, si distribuisce un server di configurazione locale per gestire la replica, si aggiunge tale server all'insieme di credenziali e si specificano le impostazioni di replica. Dopo aver abilitato la replica per i computer di cui si vuole eseguire la migrazione, si effettua un rapido failover di test. Si esegue quindi un failover non pianificato con l'opzione **Completa la migrazione**.

## <a name="migrate-aws-to-azure"></a>Eseguire la migrazione da AWS ad Azure

È possibile eseguire la migrazione di istanze AWS a VM di Azure. In questo scenario è supportata solo la migrazione. In altri termini, è possibile replicare le istanze AWS ed effettuarne il failover in Azure, ma non eseguire il failback. Ai fini della migrazione, le istanze AWS vengono gestite come i server fisici. Si configura un insieme di credenziali di Servizi di ripristino, si distribuisce un server di configurazione locale per gestire la replica, si aggiunge tale server all'insieme di credenziali e si specificano le impostazioni di replica. Dopo aver abilitato la replica per i computer di cui si vuole eseguire la migrazione, si effettua un rapido failover di test. Si esegue quindi un failover non pianificato con l'opzione **Completa la migrazione**.




## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di VM VMware ad Azure](site-recovery-vmware-to-azure.md)
- [Eseguire la migrazione di server fisici ad Azure](site-recovery-vmware-to-azure.md)
- [Eseguire la migrazione di VM Hyper-V in cloud VMM ad Azure](site-recovery-vmm-to-azure.md)
- [Eseguire la migrazione di VM Hyper-V senza VMM ad Azure](site-recovery-hyper-v-site-to-azure.md)
- [Eseguire la migrazione di VM di Azure tra aree di Azure](site-recovery-migrate-azure-to-azure.md)
- [Eseguire la migrazione di istanze AWS ad Azure](site-recovery-migrate-aws-to-azure.md)



<!--HONumber=Jan17_HO1-->


