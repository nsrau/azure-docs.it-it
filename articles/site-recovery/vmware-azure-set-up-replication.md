---
title: Configurare i criteri di replica per il ripristino di emergenza di VMware con Azure Site Recovery Documenti Microsoft
description: Descrive come configurare le impostazioni per la replica del ripristino di emergenza di VMware in Azure con Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257121"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Configurare e gestire i criteri di replica per il ripristino di emergenza di VMware

Questo articolo illustra come configurare criteri di replica quando si esegue la replica di macchine virtuali VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Creare criteri

1. Selezionare **Gestisci** > **infrastruttura site recovery**.
2. In **For VMware and Physical machines** (Per VMware e computer fisici) selezionare **Criteri di replica**.
3. Fare clic su **+Criteri di replica** e specificare il nome del criterio.
4. In **Soglia RPO**specificare il limite RPO. Quando la replica continua supera questo limite, vengono generati avvisi.
5. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo di conservazione. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore. Per l'archiviazione standard sono supportate fino a 72 ore.
6. In **Frequenza snapshot coerente**con l'app, scegliere dall'elenco a discesa la frequenza (in ore) dei punti di ripristino che contengono snapshot coerenti con l'applicazione. Se si desidera disattivare la generazione di punti di coerenza dell'applicazione, scegliere il valore "Off" nell'elenco a discesa.
7. Fare clic su **OK**. La creazione del criterio dovrebbe richiedere dai 30 secondi ai 60 secondi.

Quando si crea un criterio di replica, viene creato automaticamente un criterio di replica di failback con il suffisso "failback". Dopo aver creato il criterio, è possibile modificarlo selezionandolo e scegliendo il comando **Modifica impostazioni**.

## <a name="associate-a-configuration-server"></a>Associare un server di configurazione

Associare il criterio di replica al server di configurazione locale.

1. Fare clic su **Associa** e selezionare il server di configurazione.

    ![Associare il server di configurazione](./media/vmware-azure-set-up-replication/associate1.png)
2. Fare clic su **OK**. L'associazione del server di configurazione dovrebbe richiedere da 1 a 2 minuti.

    ![Associazione del server di configurazione](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Modificare un criterio

È possibile modificare un criterio di replica dopo averlo creato.

- Le modifiche apportate ai criteri vengono applicate a tutti i computer che utilizzano il criterio.
- Se si desidera associare i computer replicati a criteri di replica diversi, è necessario disabilitare e riattivare la protezione per i computer pertinenti.

Modificare un criterio come segue:
1. Selezionare **Gestisci** > **criteri di replica****dell'infrastruttura** > di Site Recovery .
2. Selezionare il criterio di replica che si desidera modificare.
3. Fare clic su **Modifica impostazioni**e aggiornare i campi frequenza snapshot coerenti con soglia/punto di ripristino RPO/app in base alle esigenze.
4. Se si desidera disattivare la generazione di punti di coerenza dell'applicazione, scegliere il valore "Off" nell'elenco a discesa del campo **Frequenza snapshot coerente con**l'app .
5. Fare clic su **Salva**. Il criterio deve essere aggiornato in 30-60 secondi.



## <a name="disassociate-or-delete-a-replication-policy"></a>Annullare l'associazione o eliminare un criterio di replica

1. Scegliere il criterio di replica.
    a. Per annullare l'associazione del criterio al server di configurazione, assicurarsi che nessuna macchina replicata usi il criterio. Fare quindi clic su **Annulla associazione**.
    b. Per eliminare il criterio, assicurarsi che non sia associato a un server di configurazione. Quindi, fare clic su **Elimina**. L'eliminazione richiederà da 30 a 60 secondi.
2. Fare clic su **OK**.
