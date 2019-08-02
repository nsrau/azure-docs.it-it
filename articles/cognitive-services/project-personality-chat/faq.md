---
title: Domande frequenti - Personality Chat
titlesuffix: Azure Cognitive Services
description: Domande frequenti su Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: e763e3c3731858e20226efbd354927f38c3d5d70
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704231"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Perché questo bot non risponde a tutte le domande che vengono poste, come altri chatbot?

Project Personality Chat consentirà di migliorare un bot con una breve conversazione che dimostra personalità e crea un'esperienza utente più completa. Questa funzionalità non è progettata per supportare lunghe conversazioni su argomenti non rilevanti per la funzione principale del bot. Anche se potrebbe rispondere a tutte le conversazioni, nella versione beta è limitata a domini di conversazione ridotti.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Come si può personalizzare la personalità in base alla propria identità di marchio?

Selezionare l'utente tipo più appropriato tra quelli predefiniti disponibili. Attualmente, è possibile modificare le risposte nella libreria editoriale per adattarle alle proprie esigenze. In futuro, sarà possibile caricare un set di enunciati di esempio dalla personalità scelta e trovare la versione più vicina per l'utente tipo. Esistono anche modi per ripetere il training del modello e personalizzarlo.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Questo servizio è alla base di agenti intelligenti esistenti, come Zo?

I servizi alla base di Zo, Cortana e Project Personality Chat condividono alcune tecniche simili, ma sono stack diversi. In questo servizio sono state incorporate le conoscenze apprese dalle esperienze con Zo e Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Questo servizio può causare esperienze spiacevoli per i clienti?

Per offrire un'esperienza più completa, Personality Chat può generare risposte oltre a quelle nel set di dati editoriale e prova a interpretare tutto l'input utente. Sono quindi possibili risposte apparentemente non corrette nel contesto. Sono stati implementati numerosi controlli per evitare risposte sfavorevoli, sulla base delle conoscenze acquisite per agenti intelligenti come Zo. Per impostazione predefinita, Project Personality Chat è impostato per rispondere solo a finalità dell'utente riconosciute. È consigliabile verificare se Project Personality Chat è adatto alle proprie esigenze. I clienti sono invitati a inviare commenti e suggerimenti se riscontrano situazioni che richiedono ulteriore training. Se si prevede di usare questo servizio con i clienti in futuro, è consigliabile implementare la registrazione anonimizzata per facilitare l'individuazione dei problemi con le interazioni con gli utenti in diretta.
