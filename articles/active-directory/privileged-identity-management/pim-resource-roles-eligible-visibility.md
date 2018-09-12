---
title: Assegnazioni idonee e visibilità delle risorse in PIM - Azure | Microsoft Docs
description: Questo articolo descrive come assegnare idonei per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666890"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Assegnazioni idonee e visibilità delle risorse in PIM

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

## <a name="azure-resource-role-approval-workflow"></a>Flusso di lavoro di approvazione del ruolo della risorsa di Azure

Con il flusso di lavoro di approvazione in PIM per i ruoli delle risorse di Azure, gli amministratori possono proteggere ulteriormente o limitare l'accesso alle risorse critiche, ovvero possono richiedere l'approvazione per attivare le assegnazioni di ruolo.

Il concetto di gerarchia delle risorse è specifico dei ruoli delle risorse di Azure. Questa gerarchia consente l'ereditarietà delle assegnazioni di ruolo da un oggetto risorsa padre verso il basso per tutte le risorse figlio all'interno del contenitore padre. 

Si supponga, ad esempio, che Luca, un amministratore delle risorse, usi PIM per assegnare Alice come membro idoneo al ruolo Proprietario della sottoscrizione di Contoso. Con questa assegnazione, Alice è una proprietaria idonea di tutti i contenitori di gruppi di risorse all'interno della sottoscrizione di Contoso. Alice è anche una proprietaria idonea di tutte le risorse (ad esempio, le macchine virtuali) all'interno di ogni gruppo di risorse della sottoscrizione.

Si supponga che nella sottoscrizione di Contoso esistano tre gruppi di risorse: Test Fabrikam, Sviluppo Fabrikam e Prod Fabrikam. Ognuno di questi gruppi di risorse contiene una singola macchina virtuale.

Le impostazioni PIM vengono configurate per ogni ruolo di una risorsa. Diversamente dalle assegnazioni, queste impostazioni non vengono ereditate e si applicano esclusivamente al ruolo della risorsa.

Continuando con l'esempio precedente: Luca usa PIM per richiedere che tutti i membri del ruolo Proprietario della sottoscrizione di Contoso richiedano l'approvazione per l'attivazione. Per poter proteggere le risorse nel gruppo di risorse Prod Fabrikam, Luca richiede anche l'approvazione per i membri del ruolo Proprietario per questa risorsa. L'approvazione per l'attivazione non è richiesta per i ruoli Proprietario nei gruppi Test Fabrikam e Sviluppo Fabrikam.

Quando Alice richiede l'attivazione del ruolo Proprietario per la sottoscrizione di Contoso, un responsabile approvazione deve approvare o rifiutare la richiesta prima che lei diventi attiva nel ruolo. Se Alice decide di [limitare l'ambito dell'attivazione](pim-resource-roles-activate-your-roles.md) al gruppo di risorse Prod Fabrikam, un responsabile approvazione deve approvare o rifiutare anche questa richiesta, ma, se Alice decide di estendere l'ambito dell'attivazione anche a uno o entrambi i gruppi di risorse Test Fabrikam o Sviluppo Fabrikam, l'approvazione non è necessaria.

Il flusso di lavoro di approvazione potrebbe non essere necessario per tutti i membri di un ruolo. Si consideri uno scenario in cui l'organizzazione assume diversi collaboratori a tempo determinato che contribuiscano allo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure. L'amministratore delle risorse vuole che i dipendenti siano idonei all'accesso senza approvazione, ma che per i collaboratori a tempo determinato sia richiesta l'approvazione. Per configurare il flusso di lavoro di approvazione solo per i collaboratori a tempo determinato, è possibile creare un ruolo personalizzato con le stesse autorizzazioni del ruolo assegnato ai dipendenti. È possibile richiedere l'approvazione per attivare il ruolo personalizzato. [Altre informazioni sui ruoli personalizzati](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
