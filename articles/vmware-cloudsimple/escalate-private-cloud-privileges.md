---
title: Riassegnare i privilegi del cloud privato
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive come eseguire l'escalation dei privilegi sul cloud privato per le funzioni amministrative in vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870466"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalate private Cloud privilegi di vCenter dal portale CloudSimple

Per l'accesso amministrativo al tuo vCenter cloud privato, puoi temporaneamente aumentare i tuoi privilegi CloudSimple.  Utilizzando privilegi elevati, è possibile installare soluzioni VMware, aggiungere origini di identità e gestire gli utenti.

È possibile creare nuovi utenti nel dominio SSO di vCenter e accedere a vCenter.  Quando si creano nuovi utenti, aggiungerli ai gruppi incorporati CloudSimple per l'accesso a vCenter.  Per ulteriori informazioni, vedere [Modello di autorizzazione CloudSimple Private Cloud di VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Non apportare modifiche alla configurazione per i componenti di gestione. Le azioni eseguite durante l'escalation di stato privilegiato possono influire negativamente sul sistema o rendere il sistema non disponibile.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Riassegnare i privilegi

1. Accedere al [portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **Risorse,** selezionare il cloud privato per il quale si desidera riassegnare i privilegi.

3. Nella parte inferiore della pagina Riepilogo in **Modifica privilegi vSphere**fare clic su **Escalate .**

    ![Modificare il privilegio vSphere](media/escalate-private-cloud-privilege.png)

4. Selezionare il tipo di utente vSphere.  Solo `CloudOwner@cloudsimple.local` l'utente locale può essere riassegnato.

5. Selezionare l'intervallo di tempo di riassegna dall'elenco a discesa. Scegliere il periodo più breve che consentirà di completare l'attività.

6. Selezionare la casella di controllo per confermare di aver compreso i rischi.

    ![Finestra di dialogo dei privilegi di riassegna](media/escalate-private-cloud-privilege-dialog.png)

7. Fare clic su **OK**.

8. Il processo di escalation può richiedere un paio di minuti. Al termine, fare clic su **OK**.

L'escalation dei privilegi inizia e dura fino alla fine dell'intervallo selezionato.  È possibile accedere al cloud privato vCenter per eseguire attività amministrative.

> [!IMPORTANT]
> Solo un utente può disporre di privilegi riassegnati.  È necessario riassegnare i privilegi dell'utente prima di poter riassegnare i privilegi di un altro utente.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* o *Cloud-Global-VM-Admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account di servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

## <a name="extend-privilege-escalation"></a>Estendere l'escalation dei privilegiExtend privilege escalation

Se è necessario più tempo per completare le attività, è possibile estendere il periodo di escalation dei privilegi.  Scegliere l'intervallo di tempo di riescalation aggiuntivo che consente di completare le attività amministrative.

1. Nel portale **Resources** > **Private Clouds** nel portale CloudSimple selezionare il cloud privato per il quale si desidera estendere l'escalation dei privilegi.

2. Nella parte inferiore della scheda Riepilogo fare clic su **Estendi escalation privilegi**.

    ![Estendere l'escalation dei privilegiExtend privilege escalation](media/de-escalate-private-cloud-privilege.png)

3. Selezionare un intervallo di tempo di riassegna dall'elenco a discesa. Esaminare la nuova ora di fine dell'escalation.

4. Fare clic su **Salva** per estendere l'intervallo.

## <a name="de-escalate-privileges"></a>Decennale dei privilegi

Una volta completate le attività amministrative, è necessario deimpostare i privilegi.  

1. Nel portale **Resources** > **Private Clouds** nel portale CloudSimple selezionare il cloud privato per il quale si desidera eseguire l'escalation dei privilegi.

2. Fare clic su **De-riassegna**.

3. Fare clic su **OK**.

> [!IMPORTANT]
> Per evitare errori, esci da vCenter e accedi di nuovo dopo la decrescente dei privilegi.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le origini di identità del vCenter per l'utilizzo di Active Directory](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Installare la soluzione di backup nelle macchine virtuali del carico di lavoro di [backupInstall](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/) backup solution to backup workload virtual machines