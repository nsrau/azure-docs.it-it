---
title: Azure VMware Solutions (AVS)-escalation dei privilegi AVS
description: Viene descritto come eseguire l'escalation delle autorizzazioni AVS per eseguire funzioni amministrative in AVS private cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025334"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Escalation dei privilegi AVS per eseguire funzioni amministrative in AVS private cloud vCenter

L'approccio con privilegi AVS è progettato per offrire agli utenti di vCenter i privilegi necessari per eseguire operazioni normali. In alcuni casi, un utente può richiedere privilegi aggiuntivi per eseguire un'attività specifica. È possibile inoltrare i privilegi di un utente SSO vCenter per un periodo di tempo limitato.

I motivi per i privilegi di escalation possono includere quanto segue:

* Configurazione delle origini identità
* Gestione utente
* Eliminazione del gruppo di porte distribuite
* Installazione di soluzioni vCenter (ad esempio, app di backup)
* Creazione di account di servizio

> [!WARNING]
> Le azioni eseguite nello stato con privilegi escalated possono influire negativamente sul sistema e causare la mancata disponibilità del sistema. Eseguire solo le azioni necessarie durante il periodo di escalation.

Dal portale AVS, [inoltrare i privilegi](escalate-private-cloud-privileges.md) per l'utente locale di CloudOwner in vCenter SSO. È possibile inoltrare il privilegio dell'utente remoto solo se è stato configurato un provider di identità aggiuntivo in vCenter. L'escalation dei privilegi comporta l'aggiunta dell'utente selezionato al gruppo di amministratori predefiniti di vSphere. Un solo utente può disporre di privilegi escalation. Se è necessario inoltrare i privilegi di un altro utente, prima di tutto annullare l'escalation dei privilegi degli utenti correnti.

Gli utenti di altre origini di identità devono essere aggiunti come membri del gruppo CloudOwner.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account del servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.
Durante il periodo di escalation, AVS usa il monitoraggio automatizzato con le notifiche di avviso associate per identificare eventuali modifiche accidentali all'ambiente.
