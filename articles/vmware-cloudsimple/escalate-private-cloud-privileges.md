---
title: Escalation dei privilegi del cloud privato-soluzione VMware di Azure di CloudSimple
description: Viene descritto come eseguire l'escalation dei privilegi sul cloud privato per le funzioni amministrative in vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b54f1d8c0705488c53925d097433e77aed3eac72
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617604"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalation dei privilegi di cloud privato nel portale di CloudSimple

Per l'accesso amministrativo al cloud privato, è possibile inoltrare temporaneamente i privilegi di CloudSimple.  Usando privilegi elevati, è possibile installare soluzioni VMware, aggiungere origini di identità e gestire gli utenti.

È possibile creare nuovi utenti nel dominio vCenter SSO e accedere a vCenter.  Quando si creano nuovi utenti, aggiungerli ai gruppi predefiniti di CloudSimple per l'accesso a vCenter.  Per altre informazioni, vedere il [modello di autorizzazione del cloud privato CloudSimple di VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Non apportare modifiche alla configurazione per i componenti di gestione. Le azioni eseguite durante lo stato con privilegi escalated possono avere un impatto negativo sul sistema o causare un sistema non disponibile.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Riassegnare i privilegi

1. Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** e selezionare il cloud privato per il quale si desidera eseguire l'escalation dei privilegi.

3. Nella parte inferiore della pagina Riepilogo in **Modifica privilegi vSphere**fare clic suescalation.

    ![Modificare il privilegio vSphere](media/escalate-private-cloud-privilege.png)

4. Selezionare il tipo di utente vSphere.  È `CloudOwner@cloudsimple.local` possibile inoltrare solo l'utente locale.

5. Selezionare l'intervallo di tempo di escalation dall'elenco a discesa. Scegliere il periodo più breve che consentirà di completare l'attività.

6. Selezionare la casella di controllo per confermare di avere compreso i rischi.

    ![Finestra di dialogo Privilege escalation](media/escalate-private-cloud-privilege-dialog.png)

7. Fare clic su **OK**.

8. Il processo di escalation può richiedere un paio di minuti. Al termine fare clic su **OK**.

L'escalation dei privilegi inizia e dura fino alla fine dell'intervallo selezionato.  È possibile accedere al cloud privato vCenter per eseguire attività amministrative.

> [!IMPORTANT]
> Un solo utente può disporre di privilegi escalation.  È necessario deinoltrare i privilegi dell'utente prima di poter inoltrare i privilegi di un altro utente.

## <a name="extend-privilege-escalation"></a>Estendi escalation dei privilegi

Se è necessario ulteriore tempo per completare le attività, è possibile estendere il periodo di escalation dei privilegi.  Scegliere l'intervallo di tempo di escalation aggiuntivo che consente di completare le attività amministrative.

1. Nei**cloud privati** **delle risorse** > nel portale di CloudSimple selezionare il cloud privato per il quale si vuole estendere l'escalation dei privilegi.

2. Nella parte inferiore della scheda Riepilogo fare clic su **Estendi**escalation dei privilegi.

    ![Estendi escalation dei privilegi](media/de-escalate-private-cloud-privilege.png)

3. Selezionare un intervallo di tempo di escalation dall'elenco a discesa. Esaminare la nuova ora di fine dell'escalation.

4. Fare clic su **Salva** per estendere l'intervallo.

## <a name="de-escalate-privileges"></a>Privilegi de escalation

Una volta completate le attività amministrative, è necessario annullare l'escalation dei privilegi.  

1. Nei**cloud privati** **delle risorse** > nel portale di CloudSimple selezionare il cloud privato per il quale si desidera degenerare i privilegi.

2. Fare clic su **de-** escalation.

3. Fare clic su **OK**.

> [!IMPORTANT]
> Per evitare errori, disconnettersi da vCenter ed eseguire di nuovo l'accesso dopo i privilegi di decalation.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le origini di identità vCenter da usare Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Installare la soluzione di backup per eseguire il [backup di macchine virtuali](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)