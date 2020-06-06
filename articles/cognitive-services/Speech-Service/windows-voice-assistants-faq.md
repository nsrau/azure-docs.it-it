---
title: Assistenti vocali su Windows-domande frequenti
titleSuffix: Azure Cognitive Services
description: Domande comuni che si manifestano spesso durante lo sviluppo di Windows Voice Agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457013"
---
# <a name="samples-and-faqs"></a>Esempi e domande frequenti

## <a name="the-uwp-voice-assistant-sample"></a>Esempio di Assistente vocale UWP

L'esempio UWP Voice Assistant è un assistente vocale di Windows che serve a

- dimostrazione dell'uso delle API di Windows ConversationalAgent
- visualizzare le procedure consigliate per un agente vocale in Windows
- fornire un'app adattabile e componenti riutilizzabili per l'applicazione MVP Agent
- Mostra come usare la comunicazione diretta con il Framework bot o i comandi personalizzati con le API ConversationalAgent di Windows per un'esperienza end-to-end per l'agente vocale

La documentazione fornita con l'app di esempio illustra il percorso del codice di attivazione vocale e gestione degli agenti, dai prerequisiti di avvio attraverso la pulizia corretta.

> [!div class="nextstepaction"]
> [Visitare il repository GitHub per l'esempio UWP](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Ricerca per categorie contattare Microsoft per risorse quali token di funzionalità di accesso limitato e file di modello di parole chiave?

Contattare winvoiceassistants@microsoft.com per richiedere queste risorse.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>L'app viene visualizzata in una finestra di piccole dimensioni quando viene attivata per voce. Come è possibile passare dalla visualizzazione compatta a una finestra completa dell'applicazione?

Quando l'applicazione viene attivata per la prima volta da Voice, viene avviata in una vista compatta. Leggere le [linee guida di progettazione per l'anteprima dell'attivazione vocale](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) per istruzioni sulle diverse visualizzazioni e transizioni tra di essi per gli assistenti vocali in Windows.

Per passare dalla visualizzazione compatta alla visualizzazione completa dell'app, usare l'API appView `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Perché le funzionalità di Assistente vocale in Windows sono abilitate solo per le applicazioni UWP?

Considerati i rischi per la privacy associati a funzionalità come l'attivazione vocale, le funzionalità della piattaforma UWP sono necessarie per consentire alle funzionalità di Assistente vocale in Windows di essere sufficientemente sicure.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>L'esempio UWP Voice Assistant usa il riconoscimento vocale diretto della riga. È necessario utilizzare il linguaggio direct line per l'Assistente vocale in Windows?

L'applicazione di esempio UWP è stata sviluppata con la voce Direct Line e l'SDK di servizi vocali come dimostrazione di come usare un servizio di dialogo con la funzionalità agente di conversazione di Windows. Tuttavia, è possibile usare qualsiasi servizio per la verifica delle parole chiave locali e cloud, la conversione vocale-testo, la finestra di dialogo bot e la conversione da sintesi vocale.