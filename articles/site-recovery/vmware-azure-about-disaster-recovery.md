---
title: Informazioni sul ripristino di emergenza di macchine virtuali VMware in Azure tramite Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica del ripristino di emergenza di macchine virtuali VMware in Azure tramite il servizio Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: raynew
ms.openlocfilehash: 9368ff848c9be075a08d5a80a49ffc64f5392cad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214699"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Informazioni sul ripristino di emergenza di macchine virtuali VMware in Azure

Questo articolo offre una panoramica del ripristino di emergenza di macchine virtuali VMware in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="what-is-bcdr"></a>Che cos'è BCDR?

Una strategia di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) consente di mantenere operativa l'azienda. Durante il periodo di inattività pianificato e le interruzioni impreviste, la strategia BCDR mantiene i dati sicuri e disponibili e garantisce che l'esecuzione delle app continui. Oltre alle funzionalità della piattaforma BCDR, ad esempio coppie di aree e archiviazione a disponibilità elevata, Azure offre servizi di ripristino come parte integrante della soluzione BCDR. I servizi di ripristino includono gli elementi seguenti: 

- [Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), che esegue il backup di un'istanza locale e dei dati della macchina virtuale di Azure. È possibile eseguire il backup di un file e di cartelle,di carichi di lavoro specifici o di un'intera macchina virtuale. 
- [Azure Site Recovery](site-recovery-overview.md), che offre resilienza e ripristino di emergenza per le app e carichi di lavoro in esecuzione in computer locali oppure in macchine virtuali IaaS di Azure. Site Recovery orchestra la replica, gestisce i failover in Azure quando si verificano interruzioni e gestisce anche il ripristino da Azure nel sito primario. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>In che modo Site Recovery esegue il ripristino di emergenza?

1. Dopo la preparazione di Azure e del sito locale, si imposta e abilita la replica per i computer locali.
2. Site Recovery orchestra la replica iniziale del computer in base alle impostazioni dei criteri.
3. Dopo la replica iniziale, Site Recovery replica le modifiche differenziali in Azure. 
4. Quando tutti gli elementi vengono replicati come previsto, viene eseguita un'analisi del ripristino di emergenza.
    - L'analisi consente di garantire che il failover funzioni come previsto quando si verifica un'esigenza reale.
    - Viene eseguito un failover di test senza alcun impatto sull'ambiente di produzione.
5. Se si verifica un'interruzione del servizio, si esegue un failover completo in Azure. È possibile eseguire il failover di un singolo computer oppure è possibile creare un piano di ripristino che esegua il failover di più computer contemporaneamente.
6. In caso di failover, vengono create macchine virtuali di Azure dai dati della macchina virtuale in Archiviazione di Azure. Gli utenti possono continuare ad accedere alle app e ai carichi di lavoro dalla macchina virtuale di Azure.
7. Quando il sito locale è di nuovo disponibile, si esegue il failback da Azure.
8. Dopo aver eseguito il failback per usare ancora il sito primario, si avvia nuovamente la replica di macchine virtuali locali in Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Come è possibile capire se l'ambiente in uso è adatto per il ripristino di emergenza in Azure?

Site Recovery può replicare qualsiasi carico di lavoro in esecuzione in una macchina virtuale VMware oppure in un server fisico supportato. Di seguito vengono indicati gli elementi da verificare nell'ambiente in uso.

- Se si replicano macchine virtuali VMware, si eseguono le versioni corrette dei server di virtualizzazione VMware? [Fare clic qui](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- I computer da replicare eseguono un sistema operativo supportato? [Fare clic qui](vmware-physical-azure-support-matrix.md#replicated-machines).
- Per il ripristino di emergenza di Linux, i computer eseguono un'archiviazione guest o un file system supportato? [Fare clic qui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- I computer da replicare sono conformi ai requisiti di Azure? [Fare clic qui](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- La configurazione di rete in uso è supportata? [Fare clic qui](vmware-physical-azure-support-matrix.md#network).
- La configurazione dell'archiviazione in uso è supportata? [Fare clic qui](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Cosa è necessario configurare in Azure prima di iniziare?

Per iniziare, in Azure è necessario eseguire queste operazioni:

1. Verificare che l'account di Azure disponga delle autorizzazioni per creare macchine virtuali in Azure.
2. Creare un account di archiviazione per conservare le immagini dei computer replicati.
3. Creare una rete di macchine virtuali di Azure cui aggiungere le macchine virtuali di Azure quando vengono create dall'archiviazione dopo il failover.
4. Configurare un insieme di credenziali di Servizi di ripristino di Azure per Site Recovery. L'insieme di credenziali si trova nel portale di Azure e viene usato per distribuire, configurare, orchestrare, monitorare e risolvere i problemi di distribuzione di Site Recovery.

*Ulteriore assistenza?*

Sono disponibili altre informazioni su come configurare Azure tramite la [verifica dell'account](tutorial-prepare-azure.md#verify-account-permissions), la creazione di un [account di archiviazione](tutorial-prepare-azure.md#create-a-storage-account) e di [rete](tutorial-prepare-azure.md#set-up-an-azure-network) e l'[impostazione di insieme di credenziali](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Cosa è necessario configurare in locale prima di iniziare?

Di seguito vengono indicate le operazioni da eseguire.

1. Configurare due account:

    - Se si replicano macchine virtuali VMware, è necessario un account per Site Recovery per accedere agli host vCenter SErver o vSphere ESXi per individuare automaticamente le macchine virtuali.
    - È necessario un account per installare l'agente del servizio Mobility di Site Recovery in ogni computer fisico oppure in ogni macchina virtuale da replicare.

2. Verificare la compatibilità dell'infrastruttura VMware, se non è già stato fatto in precedenza.
3. Verificare che sia possibile connettersi alle macchine virtuali di Azure dopo il failover. Configurare Remote Desktop Protocol in computer Windows locali o SSH in computer Linux.

*Ulteriore assistenza?*
- Preparare gli account per l'[individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e per l'[installazione del servizio Mobility](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Verificare](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) che le impostazioni VMware siano compatibili.
- [Prepararsi](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) in modo che sia possibile connettersi ad Azure dopo il failover.
- Se si desiderano informazioni approfondite sulla configurazione degli indirizzi IP per macchine virtuali di Azure dopo il failover, [leggere questo articolo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Come si configura il ripristino di emergenza?

Dopo aver creato l'infrastruttura di Azure e in locale, è possibile configurare il ripristino di emergenza.

1. Per comprendere i componenti da distribuire, esaminare l'[architettura di replica di VMware in Azure](vmware-azure-architecture.md) e l'[architettura di replica di server fisici in Azure](physical-azure-architecture.md). Esistono diversi componenti, pertanto è importante comprendere il modo in cui si combinano.
2. **Ambiente di origine**. Il primo passo della distribuzione consiste nel configurare l'ambiente di origine della replica. Specificare gli elementi da replicare e la posizione in cui eseguire la replica.
3. **Server di configurazione**. È necessario configurare un server di configurazione nell'ambiente di origine in locale.
    - Il server di configurazione è un singolo computer locale. Per il ripristino di emergenza di VMware, è consigliabile distribuirlo come una macchina virtuale VMware che può essere distribuito da un modello OVF scaricabile.
    - Il server di configurazione coordina le comunicazioni tra l'ambiente locale e Azure.
    - Nel server di configurazione sono in esecuzione un paio di altri componenti.
        - Il server di elaborazione riceve, ottimizza e invia i dati di replica ad Archiviazione di Azure. Il server gestisce anche l'installazione automatica del servizio Mobility nei computer da replicare ed esegue l'individuazione automatica delle macchine virtuali su server VMware.
        - Il server di destinazione master gestisce i dati di replica durante il failback da Azure.
    - La configurazione include la registrazione del server di configurazione nell'insieme di credenziali, il download di server MySQL e di VMware PowerCLI e la specifica degli account creati per l'individuazione automatica e installazione del servizio Mobility.
4. **Ambiente di destinazione**. È necessario configurare l'ambiente di Azure di destinazione specificando le impostazioni della sottoscrizione di Azure, dell'archiviazione e della rete.
5. **Criteri di replica**. Specificare come eseguire la replica. Le impostazioni includono la frequenza di creazione e di archiviazione dei punti di ripristino e indicano se devono essere creati snapshot coerenti con le app.
6. **Abilitare la replica**. Abilitare la replica per i computer locali. Se è stato creato un account per installare il servizio Mobility, tale account verrà installato quando si abilita la replica per un computer. 

*Ulteriore assistenza?*

- Per una descrizione dettagliata di tali passaggi, provare l'[esercitazione su VMware](vmware-azure-tutorial.md) e la [procedura dettagliata sui server fisici](physical-azure-disaster-recovery.md).
- [Altre informazioni](vmware-azure-set-up-source.md) sulla configurazione dell'ambiente di origine.
- [Informazioni](vmware-azure-deploy-configuration-server.md) sui requisiti del server di configurazione e sull'impostazione del server di configurazione con un modello OVF per la replica VMware. Se per qualche motivo non è possibile usare un modello o se si replicano server fisici, [usare queste istruzioni](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Altre informazioni](vmware-azure-set-up-target.md) sulle impostazioni di destinazione.
- [Altre informazioni](vmware-azure-set-up-replication.md) sulla configurazione di criteri di replica.
- [Informazioni](vmware-azure-enable-replication.md) su come abilitare la replica ed [escludere](vmware-azure-exclude-disk.md) dischi dalla replica.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Si è verificato un errore. Come è possibile risolverlo?

- Come primo passaggio, provare a [monitorare la distribuzione](site-recovery-monitor-and-troubleshoot.md) per verificare lo stato di eventuali problemi di elementi, processi e infrastruttura replicati e individuare eventuali errori.
- Se non si riesce a completare la replica iniziale o se la replica in corso non funziona come previsto, [leggere questo articolo](vmware-azure-troubleshoot-replication.md) sugli errori comuni e per trovare suggerimenti sulla risoluzione dei problemi.
- Se si verificano problemi con l'installazione automatica del servizio Mobility nei computer da replicare, esaminare gli errori comuni in [questo articolo](vmware-azure-troubleshoot-push-install.md).
- Se il failover non funziona come previsto, controllare gli errori comuni in [questo articolo](site-recovery-failover-to-azure-troubleshoot.md).
- Se il failback non funziona, controllare se il problema viene descritto in [questo articolo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Passaggi successivi

Dopo che la replica è stata completata, è opportuno [eseguire un'analisi del ripristino di emergenza](tutorial-dr-drill-azure.md) per assicurarsi che il failover funzioni come previsto. 
