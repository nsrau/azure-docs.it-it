---
title: Soluzione VMware di Azure di CloudSimple-escalation dei privilegi CloudSimple
description: Viene descritto come eseguire l'escalation delle autorizzazioni CloudSimple per eseguire funzioni amministrative nel cloud privato vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619612"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalation dei privilegi CloudSimple per eseguire funzioni amministrative nel cloud privato vCenter

L'approccio con privilegi CloudSimple è progettato per fornire agli utenti di vCenter i privilegi necessari per eseguire operazioni normali. In alcuni casi, un utente può richiedere privilegi aggiuntivi per eseguire un'attività specifica.  È possibile inoltrare i privilegi di un utente SSO vCenter per un periodo di tempo limitato.

I motivi per i privilegi di escalation possono includere quanto segue:

* Configurazione delle origini identità
* Gestione utenti
* Eliminazione del gruppo di porte distribuite
* Installazione di soluzioni vCenter (ad esempio, app di backup)
* Creazione di account di servizio

> [!WARNING]
> Le azioni eseguite nello stato con privilegi escalated possono influire negativamente sul sistema e causare la mancata disponibilità del sistema. Eseguire solo le azioni necessarie durante il periodo di escalation.

Dal portale di CloudSimple, [inoltrare i privilegi](escalate-private-cloud-privileges.md) per l'utente locale di CloudOwner in vCenter SSO.  È possibile inoltrare il privilegio dell'utente remoto solo se è stato configurato un provider di identità aggiuntivo in vCenter.  L'escalation dei privilegi comporta l'aggiunta dell'utente selezionato al gruppo di amministratori predefiniti di vSphere.  Un solo utente può disporre di privilegi escalation.  Se è necessario inoltrare i privilegi di un altro utente, prima di tutto annullare l'escalation dei privilegi degli utenti correnti.

Gli utenti di altre origini di identità devono essere aggiunti come membri del gruppo CloudOwner.

Durante il periodo di escalation, CloudSimple usa il monitoraggio automatizzato con le notifiche di avviso associate per identificare eventuali modifiche accidentali all'ambiente.
