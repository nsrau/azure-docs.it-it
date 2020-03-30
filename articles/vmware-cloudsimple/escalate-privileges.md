---
title: Azure VMware Solution by CloudSimple - Escalate CloudSimple privilegi
description: Descrive come eseguire l'escalation delle autorizzazioni CloudSimple per eseguire funzioni amministrative nel vCenter del cloud privato
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025334"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eseguire l'escalation di CloudSimple privilegi per eseguire funzioni amministrative in Private Cloud vCenter

L'approccio ai privilegi CloudSimple è progettato per fornire agli utenti di vCenter i privilegi necessari per eseguire le normali operazioni. In alcuni casi, un utente potrebbe richiedere privilegi aggiuntivi per eseguire una determinata attività.  È possibile riassegnare i privilegi di un utente SSO di vCenter per un periodo limitato.

I motivi per l'escalation dei privilegi possono includere quanto segue:

* Configurazione delle origini di identità
* Gestione utente
* Eliminazione del gruppo di porte distribuite
* Installazione di soluzioni vCenter (ad esempio app di backup)
* Creazione degli account di servizio

> [!WARNING]
> Le azioni eseguite nello stato privilegiato erida possono influire negativamente sul sistema e possono rendere il sistema non disponibile. Eseguire solo le azioni necessarie durante il periodo di escalation.

Dal portale CloudSimple, [riassegnare i privilegi](escalate-private-cloud-privileges.md) per l'utente locale CloudOwner nel servizio SSO di vCenter.  È possibile riassegnare i privilegi dell'utente remoto solo se in vCenter è configurato un provider di identità aggiuntivo.  L'escalation dei privilegi comporta l'aggiunta dell'utente selezionato al gruppo Administrators predefinito vSphere.  Solo un utente può disporre di privilegi riassegnati.  Se è necessario riassegnare i privilegi di un altro utente, riassegnare innanzitutto i privilegi degli utenti correnti.

Gli utenti provenienti da origini di identità aggiuntive devono essere aggiunti come membri del gruppo CloudOwner.Users from additional identity sources must be added as members of CloudOwner group.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* o *Cloud-Global-VM-Admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account di servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

Durante il periodo di escalation, CloudSimple utilizza il monitoraggio automatico con notifiche di avviso associate per identificare eventuali modifiche involontarie all'ambiente.
