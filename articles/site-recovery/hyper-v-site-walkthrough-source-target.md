---
title: Configurare l'origine e la destinazione per la replica Hyper-V in Azure (senza System Center VMM) con Azure Site Recovery| Microsoft Docs
description: Vengono riepilogati i passaggi per configurare le impostazioni di origine e di destinazione per la replica di VM Hyper-V in Archiviazione di Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>Passaggio 8: Configurare l'origine e la destinazione per la replica Hyper-V in Azure

Questo articolo illustra come configurare le impostazioni di origine e di destinazione per la replica di macchine virtuali Hyper-V locali (senza System Center VMM) in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il sito Hyper-V, installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure negli host Hyper-V e registrare il sito nell'insieme di credenziali.

1. In **Preparare l'infrastruttura** fare clic su **Origine**. Per aggiungere un nuovo sito Hyper-V come contenitore per i cluster o gli host Hyper-V, fare clic su **+ Sito Hyper-V**.

    ![Impostare l'origine](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. In **Crea il sito Hyper-V** specificare un nome per il sito. Fare quindi clic su **OK**. A questo punto, selezionare il sito creato e fare clic su **+Server Hyper-V** per aggiungere un server al sito.

    ![Impostare l'origine](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. In **Aggiungi server** > **Tipo di server** verificare che sia visualizzato **Server Hyper-V**.

    - Verificare che il server Hyper-V che si desidera aggiungere sia conforme ai [prerequisiti](#on-premises-prerequisites) e sia in grado di accedere agli URL specificati.
    - Scaricare il file di installazione del provider di Azure Site Recovery. Eseguire questo file per installare il provider e l'agente di Servizi di ripristino in ogni host Hyper-V.

    ![Impostare l'origine](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Installare provider e agente

1. Eseguire il file di installazione del provider in ogni host aggiunto al sito Hyper-V. Se l'installazione viene eseguita in un cluster Hyper-V, eseguire il file di installazione in ogni nodo del cluster. L'installazione e la registrazione di ogni nodo del cluster Hyper-V garantisce che le macchine virtuali siano protette anche se ne viene eseguita la migrazione tra i nodi.
2. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
3. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.
4. In **Impostazioni dell'insieme di credenziali** fare clic su **Esplora** per selezionare il file di chiave dell'insieme di credenziali scaricato. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.

    ![Server registration](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. In **Impostazioni proxy** specificare in che modo il provider in esecuzione negli host Hyper-V si connette ad Azure Site Recovery tramite Internet.

    * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    * Se per il proxy esistente è necessaria l'autenticazione o si desidera usare un proxy personalizzato per la connessione al provider, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
    * Se si usa un proxy:
        - Specificare l'indirizzo, la porta e le credenziali.
        - Assicurarsi che sia possibile accedere tramite il proxy agli URL indicati nei [prerequisiti](#prerequisites) .

    ![Internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'insieme di credenziali.

    ![Percorso di installazione](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. Al termine della registrazione, i metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **Site Recovery Infrastructure** >  (Infrastruttura di Site Recovery)**Hyper-V Hosts** (Host Hyper-V).


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Specificare l'account di archiviazione di Azure per la replica e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di Azure dopo il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le macchine virtuali.

3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

    - Se non si dispone di un account di archiviazione, fare clic su **+ Archiviazione** per creare un account basato su Resource Manager inline. 
    - Se non si dispone di una rete Azure, fare clic su **+Rete** per creare una rete basata su Resource Manager inline.






## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 9: Configurare i criteri di replica](hyper-v-site-walkthrough-replication.md)
