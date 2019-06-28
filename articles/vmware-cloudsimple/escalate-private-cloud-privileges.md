---
title: L'escalation dei privilegi basata su cloud privato - Azure VMware Solution by CloudSimple
description: Viene descritto come eseguire l'escalation dei privilegi sul cloud privato per le funzioni amministrative in vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333029"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>L'escalation dei privilegi di Cloud privato vCenter dal portale CloudSimple 

Per l'accesso amministrativo al server vCenter sul Cloud privato, è possibile inoltrare temporaneamente i privilegi CloudSimple.  Con privilegi elevati, è possibile installare le soluzioni di VMware, aggiungere origini di identità e la gestione degli utenti.

Nuovi utenti possono essere creati nel dominio SSO vCenter e concesso l'accesso a vCenter.  Quando si creano nuovi utenti, aggiungerli ai gruppi di builtin CloudSimple per l'accesso a vCenter.  Per altre informazioni, vedere [modello di autorizzazione del Cloud privato Microsoft CloudSimple di VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Non apportare alcuna modifica di configurazione per i componenti di gestione. Le azioni eseguite durante lo stato con privilegi alzati di livello possono influire negativamente del sistema o possono causare il sistema potrebbe diventare non disponibile.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Riassegnare i privilegi

1. Accesso di [CloudSimple portale](access-cloudsimple-portal.md).

2. Aprire il **risorse** pagina, selezionare il Cloud privato per cui si desidera eseguire l'escalation dei privilegi.

3. Nella parte inferiore della pagina di riepilogo sotto **modificare i diritti di vSphere**, fare clic su **escalation**.

    ![Modificare il privilegio di vSphere](media/escalate-private-cloud-privilege.png)

4. Selezionare il tipo di utente di vSphere.  Solo **CloudOwner@cloudsimple.local** utente locali possa essere soggetti a escalation.

5. Selezionare l'intervallo di tempo escalation dall'elenco a discesa. Scegliere il periodo più breve che è possibile completare l'attività.

6. Selezionare la casella di controllo per confermare di aver compreso i rischi.

    ![Finestra di dialogo con privilegi di escalation](media/escalate-private-cloud-privilege-dialog.png)

7. Fare clic su **OK**.

8. Il processo di escalation dei blocchi può richiedere alcuni minuti. Al termine fare clic su **OK**.

L'escalation dei privilegi inizia e dura fino alla fine dell'intervallo selezionato.  È possibile accedere al server vCenter al cloud privato per eseguire attività amministrative.

> [!IMPORTANT]
> Solo un utente può avere privilegi alzati di livello.  Deprovisioning necessario riassegnare i privilegi dell'utente prima che è possibile riassegnare i privilegi di un altro utente.

## <a name="extend-privilege-escalation"></a>Estendere l'escalation dei privilegi

Se è necessaria ulteriore tempo per completare le attività, è possibile estendere il periodo di escalation dei privilegi.  Scegliere che l'escalation altro intervallo di tempo che consente di completare le attività amministrative.

1. Nel **le risorse** > **cloud privati** nel portale di CloudSimple, selezionare il Cloud privato per il quale si desidera estendere l'escalation dei privilegi.

2. Nella parte inferiore della scheda Riepilogo, fare clic su **estendere l'escalation dei privilegi**.

    ![Estendere l'escalation dei privilegi](media/de-escalate-private-cloud-privilege.png)

3. Selezionare un intervallo di tempo escalation dall'elenco a discesa. Rivedere la nuova ora di fine escalation.

4. Fare clic su **salvare** per estendere l'intervallo.

## <a name="de-escalate-privileges"></a>Annullare l'escalation dei privilegi

Dopo aver complete le attività amministrative, è necessario inoltrare deprovisioning dei privilegi.  

1. Nel **le risorse** > **cloud privati** nel portale di CloudSimple, selezionare il Cloud privato per il quale si desidera annullare l'escalation dei privilegi.

2. Fare clic su **deprovisioning l'escalation**.

3. Fare clic su **OK**.

> [!IMPORTANT]
> Per evitare eventuali errori, disconnettersi da vCenter e accedere nuovamente dopo deprovisioning escalation dei privilegi.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le origini di identità di vCenter per usare Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Installare una soluzione di backup su [backup di macchine virtuali del carico di lavoro](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)