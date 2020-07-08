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
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025334"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalation dei privilegi CloudSimple per eseguire funzioni amministrative nel cloud privato vCenter

L'approccio con privilegi CloudSimple è progettato per fornire agli utenti di vCenter i privilegi necessari per eseguire operazioni normali. In alcuni casi, un utente può richiedere privilegi aggiuntivi per eseguire un'attività specifica.  È possibile inoltrare i privilegi di un utente SSO vCenter per un periodo di tempo limitato.

I motivi per i privilegi di escalation possono includere quanto segue:

* Configurazione delle origini identità
* Gestione utente
* Eliminazione del gruppo di porte distribuite
* Installazione di soluzioni vCenter (ad esempio, app di backup)
* Creazione degli account di servizio

> [!WARNING]
> Le azioni eseguite nello stato con privilegi escalated possono influire negativamente sul sistema e causare la mancata disponibilità del sistema. Eseguire solo le azioni necessarie durante il periodo di escalation.

Dal portale di CloudSimple, [inoltrare i privilegi](escalate-private-cloud-privileges.md) per l'utente locale di CloudOwner in vCenter SSO.  È possibile inoltrare il privilegio dell'utente remoto solo se è stato configurato un provider di identità aggiuntivo in vCenter.  L'escalation dei privilegi comporta l'aggiunta dell'utente selezionato al gruppo di amministratori predefiniti di vSphere.  Un solo utente può disporre di privilegi escalation.  Se è necessario inoltrare i privilegi di un altro utente, prima di tutto annullare l'escalation dei privilegi degli utenti correnti.

Gli utenti di altre origini di identità devono essere aggiunti come membri del gruppo CloudOwner.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account del servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

Durante il periodo di escalation, CloudSimple usa il monitoraggio automatizzato con le notifiche di avviso associate per identificare eventuali modifiche accidentali all'ambiente.
