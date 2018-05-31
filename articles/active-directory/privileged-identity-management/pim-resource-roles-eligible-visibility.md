---
title: Assegnazioni idonee e visibilità delle risorse per Azure in Privileged Identity Management | Microsoft Docs
description: Questo articolo descrive come assegnare membri come idonei ai ruoli delle risorse quando si usa PIM.
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
ms.openlocfilehash: 4804d930a98192d64245784058920eeba7d30212
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149987"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Assegnazioni idonee e visibilità delle risorse con Privileged Identity Management

Privileged Identity Management (PIM) per i ruoli delle risorse di Azure offre livelli di sicurezza avanzati per le organizzazioni con risorse di Azure critiche. Gli amministratori delle risorse possono usare PIM per assegnare membri come idonei ai ruoli delle risorse. Altre informazioni sui diversi tipi e stati di assegnazione per i ruoli delle risorse di Azure sono disponibili nelle sezioni seguenti. 

## <a name="assignment-types"></a>Tipi di assegnazione

PIM per le risorse di Azure offre due tipi distinti di assegnazione:

- Idoneo
- Attivo

Le assegnazioni di tipo Idoneo richiedono al membro del ruolo eseguire un'azione per usare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori, l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili approvazione designati.

Le assegnazioni di tipo Attivo non richiedono al membro di eseguire alcuna azione per usare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Gli amministratori delle risorse possono scegliere tra due opzioni per ogni tipo di assegnazione durante la configurazione delle impostazioni PIM per un ruolo. Queste opzioni diventano la durata massima predefinita quando un membro viene assegnato al ruolo in PIM. 

Un amministratore può scegliere uno di questi tipi di assegnazione:

- Allow permanent eligible assignment (Consenti assegnazione idonea permanente)
- Allow permanent active assignment (Consenti assegnazione attiva permanente)

In alternativa, un amministratore può scegliere uno di questi tipi di assegnazione:

- Expire eligible assignments after (Scadenza delle assegnazioni idonee dopo)
- Expire active assignments after (Scadenza delle assegnazioni attive dopo)

Se un amministratore delle risorse sceglie **Allow permanent eligible assignment** (Consenti assegnazione idonea permanente) o **Allow permanent active assignment** (Consenti assegnazione attiva permanente), tutti gli amministratori che assegnano membri alla risorsa avranno la possibilità di assegnare appartenenze permanenti.

Se sceglie **Expire eligible assignments after** (Scadenza delle assegnazioni idonee dopo) o **Expire active assignments after** (Scadenza delle assegnazioni attive dopo), l'amministratore delle risorse controlla il ciclo di vita delle assegnazioni richiedendo di specificare una data di inizio e di fine per tutte le assegnazioni.

> [!NOTE] 
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse. Inoltre i membri possono avviare richieste self-service per [estendere o rinnovare le assegnazioni](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stati delle assegnazioni

PIM per le risorse di Azure include due stati delle assegnazioni distinti che vengono visualizzati nella scheda **Ruoli attivi** nelle visualizzazioni **Ruoli personali**, **Ruoli** e **Membri** di PIM. Questi stati sono:

- Assegnato
- Attivato

Quando si visualizza un'appartenenza elencata in **Ruoli attivi**, è possibile usare il valore nella colonna **Stato** per distinguere tra gli utenti assegnati come attivi (**Assegnato**) e gli utenti che hanno attivato un'assegnazione idonea (**Attivato**) e che ora sono attivi.

## <a name="next-steps"></a>Passaggi successivi

[Assegnare ruoli in Privileged Identity Manager](pim-resource-roles-assign-roles.md)
