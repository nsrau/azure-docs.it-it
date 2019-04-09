---
title: Configurare e gestire i criteri per la replica del ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare le impostazioni per la replica del ripristino di emergenza di VMware in Azure con Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b60d8a8fb9b9300a6914ad33b2f760fb5adde3b4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278225"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Configurare e gestire i criteri per la replica del ripristino di emergenza di VMware in Azure
Questo articolo illustra come configurare criteri di replica quando si esegue la replica di macchine virtuali VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Creare un criterio

1. Selezionare **Gestisci** > **Site Recovery Infrastructure** (Infrastruttura di Site Recovery).
2. In **For VMware and Physical machines** (Per VMware e computer fisici) selezionare **Criteri di replica**.
3. Fare clic su **+Criteri di replica** e specificare il nome del criterio.
4. In **Soglia RPO**specificare il limite per RPO. Quando la replica continua supera questo limite, vengono generati avvisi.
5. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo di conservazione. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore. Per l'archiviazione standard sono supportate fino a 72 ore.
6. Nelle **frequenza snapshot coerenti con l'App**, scegliere dall'elenco a discesa frequenza (in ore) punti di ripristino contenenti snapshot coerenti con l'applicazione devono essere creati. Se si vuole disattivare la generazione di punti di coerenza dell'applicazione, scegliere "Off" valore nell'elenco a discesa.
7. Fare clic su **OK**. La creazione del criterio dovrebbe richiedere dai 30 secondi ai 60 secondi.

Quando si crea un criterio di replica, viene creato automaticamente un criterio di replica di failback con il suffisso "failback". Dopo aver creato il criterio, è possibile modificarlo selezionandolo e scegliendo il comando **Modifica impostazioni**.

## <a name="associate-a-configuration-server"></a>Associare un server di configurazione

Associare il criterio di replica al server di configurazione locale.

1. Fare clic su **Associa** e selezionare il server di configurazione.

    ![Associare il server di configurazione](./media/vmware-azure-set-up-replication/associate1.png)
2. Fare clic su **OK**. L'associazione del server di configurazione dovrebbe richiedere da 1 a 2 minuti.

    ![Associazione del server di configurazione](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Modificare un criterio

1. Selezionare **gestire** > **infrastruttura di Site Recovery** > **i criteri di replica**.
2. Selezionare il criterio di replica da modificare.
3. Fare clic su **modificare le impostazioni**e aggiornare i campi di frequenza RPO/ripristino della soglia punto conservazione snapshot ore/coerenti con l'app in base alle esigenze.
4. Se si vuole disattivare la generazione di punti di coerenza dell'applicazione, scegliere "Off" valore nell'elenco a discesa del campo **frequenza snapshot coerenti con l'App**.
5. Fare clic su **Save**. I criteri devono essere aggiornati in 30 e 60 secondi.

## <a name="disassociate-or-delete-a-replication-policy"></a>Annullare l'associazione o eliminare un criterio di replica

1. Scegliere il criterio di replica.
    a. Per annullare l'associazione del criterio al server di configurazione, assicurarsi che nessuna macchina replicata usi il criterio. Fare quindi clic su **Annulla associazione**.
    b. Per eliminare il criterio, assicurarsi che non sia associato a un server di configurazione. Fare quindi clic su **Elimina**. L'eliminazione richiederà da 30 a 60 secondi.
2. Fare clic su **OK**.
