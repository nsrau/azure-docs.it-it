---
title: Configurare e gestire i criteri per la replica del ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare le impostazioni per la replica del ripristino di emergenza di VMware in Azure con Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: e83b15b5e53a203a3583c02565ea9c316b7c481c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832375"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Configurare e gestire i criteri per la replica del ripristino di emergenza di VMware in Azure
Questo articolo illustra come configurare criteri di replica quando si esegue la replica di macchine virtuali VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Creare un criterio

1. Selezionare **Gestisci** > **Site Recovery Infrastructure** (Infrastruttura di Site Recovery).
1. In **For VMware and Physical machines** (Per VMware e computer fisici) selezionare **Criteri di replica**. 
1. Fare clic su **+Criteri di replica** e specificare il nome del criterio.
1. In **Soglia RPO**specificare il limite per RPO. Quando la replica continua supera questo limite, vengono generati avvisi.
1. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo di conservazione. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore. Per l'archiviazione standard sono supportate fino a 72 ore.
1. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione.
1. Fare clic su **OK**. La creazione del criterio dovrebbe richiedere dai 30 secondi ai 60 secondi.

Quando si crea un criterio di replica, viene creato automaticamente un criterio di replica di failback con il suffisso "failback". Dopo aver creato il criterio, è possibile modificarlo selezionandolo e scegliendo il comando **Modifica impostazioni**.

## <a name="associate-a-configuration-server"></a>Associare un server di configurazione 

Associare il criterio di replica al server di configurazione locale.

1. Fare clic su **Associa** e selezionare il server di configurazione.

    ![Associare il server di configurazione](./media/vmware-azure-set-up-replication/associate1.png)

1. Fare clic su **OK**. L'associazione del server di configurazione dovrebbe richiedere da 1 a 2 minuti.

    ![Associazione del server di configurazione](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Annullare l'associazione o eliminare un criterio di replica
1. Scegliere il criterio di replica.
    a. Per annullare l'associazione del criterio al server di configurazione, assicurarsi che nessuna macchina replicata usi il criterio. Fare quindi clic su **Annulla associazione**.
    b. Per eliminare il criterio, assicurarsi che non sia associato a un server di configurazione. Fare quindi clic su **Elimina**. L'eliminazione richiederà da 30 a 60 secondi.
1. Fare clic su **OK**.
