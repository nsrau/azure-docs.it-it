---
title: Come eseguire il failback da Azure in VMware | Microsoft Docs
description: "Dopo il failover delle macchine virtuali in Azure, è possibile avviare il failback per riportarle in locale. Informazioni sui passaggi per il failback."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 50dccf6196b7affd3d21087f851b929d0e850f6d
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Eseguire il failback da Azure a un sito locale

Questo articolo descrive come eseguire il failback da Macchine virtuali di Azure all'ambiente VMware locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo avere effettuato il failover dal sito locale ad Azure seguendo l'esercitazione [Failover in Site Recovery](site-recovery-failover.md).

## <a name="prerequisites"></a>prerequisiti
- Leggere i dettagli sui [diversi tipi di failback](concepts-types-of-failback.md) e le rispettive avvertenze.

> [!WARNING]
> Non è possibile eseguire il failback se è stata [completata la migrazione](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), la macchina virtuale è stata spostata in un altro gruppo di risorse o la macchina virtuale di Azure è stata eliminata. Se si disabilita la protezione della macchina virtuale, non è possibile eseguire il failback.

> [!WARNING]
> Non è possibile eseguire il failback di un server fisico Windows Server 2008 R2 SP1 protetto e sottoposto a failover in Azure.

> [!NOTE]
> Se è stato eseguito il failover delle macchine virtuali VMware, non è possibile eseguire il failback a un host Hyper-V.


- Prima di proseguire, completare i passaggi di riprotezione in modo che le macchine virtuali siano nello stato replicato e sia possibile avviare un failover nel sito locale. Per altre informazioni, vedere [Come eseguire la riprotezione da Azure al sito locale](site-recovery-how-to-reprotect.md).

- Assicurarsi che vCenter sia in uno stato connesso prima di eseguire un failback. In caso contrario, la disconnessione dei dischi e il ricollegamento alla macchina virtuale non riescono.

- Durante il failover ad Azure, il sito locale potrebbe non essere accessibile e pertanto il server di configurazione può essere non disponibile o venire arrestato. Durante la riprotezione e il failback, il server di configurazione locale deve essere in esecuzione e in stato OK connesso. 

- Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione; in caso contrario il failback non ha esisto negativo. Assicurarsi quindi di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con l'indirizzo IP originale, in modo che il failback funzioni.

- Il server di destinazione master non deve avere snapshot prima di attivare la riprotezione o il failback.

## <a name="overview-of-failback"></a>Panoramica del failback
Dopo avere effettuato il failover ad Azure, è possibile eseguire il failback nel sito locale eseguendo le operazioni seguenti:

1. [Riproteggere](site-recovery-how-to-reprotect.md) le macchine virtuali in Azure in modo che inizino a replicare le macchine virtuali VMware nel sito locale. Come parte di questo processo, è necessario:
    1. Configurare una destinazione master locale: la destinazione master Windows per le macchine virtuali Windows e la [destinazione master Linux](site-recovery-how-to-install-linux-master-target.md) per le macchine virtuali Linux.
    2. Configurare un [server di elaborazione](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Avviare la [riprotezione](site-recovery-how-to-reprotect.md). Verrà spenta la macchina virtuale locale e i dati della macchina virtuale Azure verranno sincronizzati con i dischi locali.

1. Dopo la replica delle macchine virtuali in Azure nel sito locale, avviare un failover da Azure al sito locale.

1. Dopo avere effettuato il failback dei dati, abilitare nuovamente la riprotezione delle macchine virtuali locali, in modo che inizino la replica in Azure.

Per una rapida panoramica, guardare il video seguente che illustra come effettuare il failback a un sito locale.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Passaggi per eseguire il failback

> [!IMPORTANT]
> Prima di avviare il failback, assicurarsi di aver completato la riprotezione delle macchine virtuali. Le macchine virtuali devono essere in stato protetto e l'integrità deve essere **OK**. Per riproteggere le macchine virtuali, leggere [come eseguire la riprotezione](site-recovery-how-to-reprotect.md).

1. Nella pagina degli elementi replicati selezionare la macchina virtuale e con il pulsante destro del mouse selezionare **Failover non pianificato**.
2. In **Conferma failover** verificare la direzione del failover (da Azure) e selezionare il punto di recupero (più recente o quello coerente con l'app più recente) da usare per il failover. Il punto coerente con l'app deve essere antecedente all'ultimo punto di temporizzazione e causerà la perdita di alcuni dati.
3. Durante il failover, Site Recovery arresta le macchine virtuali in Azure. Assicurarsi che il failback sia stato completato come previsto e quindi verificare che le macchine virtuali di Azure siano state arrestate.
4. Il **commit** è richiesto per rimuovere la macchina virtuale di cui è stato effettuato il failover da Azure. Fare clic con il pulsante destro del mouse sull'elemento protetto e quindi scegliere **Esegui commit**. Un processo rimuoverà le macchine virtuali di cui è stato eseguito il failover in Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Punto di recupero in cui è possibile eseguire il failback delle macchine virtuali

Durante il failback sono disponibili due opzioni per il failback di macchine virtuali/piano di ripristino.

- Se si seleziona l'ultimo periodo di tempo elaborato, verrà eseguito il failover di tutte le macchine virtuali nell'ultimo periodo di tempo disponibile. Se è presente un gruppo di replica nel piano di ripristino, verrà effettuato il failover di ogni macchina virtuale del gruppo di replica nell'ultimo periodo di tempo indipendente.

    Non è possibile eseguire il failback di una macchina virtuale finché non ha almeno un punto di recupero. Non è possibile eseguire il failback di un piano di ripristino finché tutte le macchine virtuali non hanno almeno un punto di recupero.

> [!NOTE]
> Il punto di recupero più recente è un punto di recupero coerente con l'arresto anomalo.

- Se si seleziona il punto di recupero coerente con l'applicazione, un singolo failback della macchina virtuale ripristina l'ultimo punto di recupero coerente con l'applicazione più recente disponibile. In caso di piano di ripristino con un gruppo di replica, ogni gruppo di replica ripristina il punto di recupero disponibile comune.
I punti di recupero coerenti con l'applicazione possono essere indietro nel tempo e potrebbero verificarsi perdite di dati.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Che cosa succede agli strumenti VMware dopo il failback?

Nel caso di una macchina virtuale Windows, Azure Site Recovery disabilita gli strumenti VMware durante il failover. Durante il failback della macchina virtuale Windows, gli strumenti VMware vengono riabilitati. 


## <a name="reprotect-from-on-premises-to-azure"></a>Abilitare la riprotezione dall'ambiente locale ad Azure
Al termine del failback e dopo avere iniziato il commit, le macchine virtuali ripristinate in Azure vengono eliminate. A questo punto la macchina virtuale si trova nuovamente nel sito locale, ma non è protetta. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1. In **Insieme di credenziali** > **Impostazioni** > **Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e quindi fare clic su **Riproteggi**.
2. Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3. Fare clic su **OK** per avviare il processo di riprotezione.

> [!NOTE]
> Dopo l'avvio di una macchina virtuale in locale, registrare di nuovo l'agente nel server di configurazione può richiedere tempo, fino a 15 minuti. Durante questo intervallo di tempo la riprotezione non riuscirà e sarà visualizzato un messaggio di errore con l'avviso che l'agente non è installato. Attendere alcuni minuti e ripetere l'operazione di riprotezione.

## <a name="next-steps"></a>Passaggi successivi

Al termine del processo di riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire il [failover](site-recovery-failover.md) per spostare nuovamente le macchine virtuali in Azure.


