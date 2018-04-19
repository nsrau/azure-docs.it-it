---
title: Privileged Identity Management per le risorse di Azure - Assegnazioni idonee e visibilità delle risorse | Microsoft Docs
description: Descrive come assegnare membri come idonei ai ruoli delle risorse.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Assegnazioni idonee e visibilità delle risorse

PIM per i ruoli delle risorse di Azure offre livelli di sicurezza avanzati per le organizzazioni con risorse di Azure critiche. PIM offre agli amministratori delle risorse la possibilità di assegnare membri come idonei per i ruoli delle risorse. Di seguito sono disponibili altre informazioni sui diversi tipi di assegnazione e di stati per i ruoli delle risorse di Azure. 

## <a name="assignment-types"></a>Tipi di assegnazione

PIM per le risorse di Azure offre due tipi distinti di assegnazione:

- Idoneo
- Attivo

Le assegnazioni di tipo Idoneo richiedono al membro del ruolo eseguire un'azione per usare il ruolo. Queste azioni possono includere il completamento di un controllo Multi-Factor Authentication, la fornitura di una giustificazione e la richiesta dell'approvazione da parte di responsabili approvazione designati.

Le assegnazioni di tipo Attivo non richiedono al membro di eseguire alcuna azione per usare il ruolo. I membri assegnati come Attivo dispongono dei privilegi forniti dal ruolo in qualsiasi momento.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Gli amministratori delle risorse possono scegliere una di due opzioni per ogni tipo di assegnazione durante la configurazione delle impostazioni PIM per un ruolo. Queste opzioni diventano la durata massima predefinita quando un membro viene assegnato al ruolo in PIM.

- Allow permanent eligible assignment (Consenti assegnazione idonea permanente)
- Allow permanent active assignment (Consenti assegnazione attiva permanente)

oppure

- Expire eligible assignments after (Scadenza delle assegnazioni idonee dopo)
- Expire active assignments after (Scadenza delle assegnazioni attive dopo)

Se un amministratore di risorse sceglie "Allow permanent eligible assignment" (Consenti assegnazione idonea permanente) e/o "Allow permanent active assignment" (Consenti assegnazione attiva permanente), tutti gli amministratori che assegnano membri alla risorsa avranno la possibilità di assegnare appartenenze permanenti.

La scelta di "Expire eligible assignments after" (Scadenza delle assegnazioni idonee dopo) e/o "Expire active assignments after" (Scadenza delle assegnazioni attive dopo) consente il controllo del ciclo di vita delle assegnazioni richiedendo di specificare una data di inizio e di fine per tutte le assegnazioni.

>[!NOTE] 
>Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse e i membri possono avviare richieste self-service per [estendere o rinnovare le assegnazioni](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stati delle assegnazioni

PIM per le risorse di Azure include due stati delle assegnazioni distinti che vengono visualizzati nella scheda "Ruoli attivi" nelle visualizzazioni Ruoli personali, Ruoli e Membri di PIM. Questi stati sono:

- Assegnato
- Attivato

Quando si visualizza un'appartenenza elencata in "Ruoli attivi", la colonna "Stato" consente di distinguere tra gli utenti assegnati come attivi (Assegnato) e gli utenti che hanno attivato un'assegnazione idonea e sono ora attivi (Attivato).

## <a name="next-steps"></a>Passaggi successivi

[Assegnare i ruoli in PIM](pim-resource-roles-assign-roles.md)

