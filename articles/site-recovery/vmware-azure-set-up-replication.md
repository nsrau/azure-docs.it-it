---
title: Configurare i criteri di replica per il ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare le impostazioni per la replica del ripristino di emergenza di VMware in Azure con Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954331"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Configurare e gestire i criteri di replica per il ripristino di emergenza di VMware

Questo articolo illustra come configurare criteri di replica quando si esegue la replica di macchine virtuali VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Creare un criterio

1. Selezionare **Gestisci** > **Site Recovery Infrastructure** (Infrastruttura di Site Recovery).
2. In **For VMware and Physical machines** (Per VMware e computer fisici) selezionare **Criteri di replica**.
3. Fare clic su **+Criteri di replica** e specificare il nome del criterio.
4. In **Soglia RPO**specificare il limite per RPO. Quando la replica continua supera questo limite, vengono generati avvisi.
5. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo di conservazione. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore. Per l'archiviazione standard sono supportate fino a 72 ore.
6. In **frequenza snapshot coerenti**con l'app scegliere dall'elenco a discesa la frequenza, espressa in ore, in cui devono essere creati i punti di ripristino contenenti snapshot coerenti con l'applicazione. Se si desidera disattivare la generazione di punti di coerenza dell'applicazione, scegliere valore "disattivato" nell'elenco a discesa.
7. Fare clic su **OK**. La creazione del criterio dovrebbe richiedere dai 30 secondi ai 60 secondi.

Quando si crea un criterio di replica, viene creato automaticamente un criterio di replica di failback con il suffisso "failback". Dopo aver creato il criterio, è possibile modificarlo selezionandolo e scegliendo il comando **Modifica impostazioni**.

## <a name="associate-a-configuration-server"></a>Associare un server di configurazione

Associare il criterio di replica al server di configurazione locale.

1. Fare clic su **Associa** e selezionare il server di configurazione.

    ![Associare il server di configurazione](./media/vmware-azure-set-up-replication/associate1.png)
2. Fare clic su **OK**. L'associazione del server di configurazione dovrebbe richiedere da 1 a 2 minuti.

    ![Associazione del server di configurazione](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Modificare un criterio

È possibile modificare i criteri di replica dopo la creazione.

- Le modifiche ai criteri vengono applicate a tutti i computer che usano i criteri.
- Se si desidera associare computer replicati a criteri di replica diversi, è necessario disabilitare e riabilitare la protezione per i computer pertinenti.

Modificare i criteri come indicato di seguito:
1. Selezionare **gestisci** > **infrastruttura Site Recovery** > **criteri di replica**.
2. Selezionare i criteri di replica che si desidera modificare.
3. Fare clic su **Modifica impostazioni**e aggiornare i campi soglia RPO/ore di conservazione del punto di ripristino/frequenza snapshot coerenti con l'app secondo le esigenze.
4. Se si desidera disattivare la generazione di punti di coerenza dell'applicazione, scegliere valore "disattivato" nell'elenco a discesa della **frequenza di snapshot coerenti**con l'app campo.
5. Fare clic su **Save**. Il criterio deve essere aggiornato da 30 a 60 secondi.



## <a name="disassociate-or-delete-a-replication-policy"></a>Annullare l'associazione o eliminare un criterio di replica

1. Scegliere il criterio di replica.
    a. Per annullare l'associazione del criterio al server di configurazione, assicurarsi che nessuna macchina replicata usi il criterio. Fare quindi clic su **Annulla associazione**.
    b. Per eliminare il criterio, assicurarsi che non sia associato a un server di configurazione. Fare quindi clic su **Elimina**. L'eliminazione richiederà da 30 a 60 secondi.
2. Fare clic su **OK**.
