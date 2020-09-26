---
title: Parole chiave personalizzate-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Cenni preliminari sulle funzionalità, le funzionalità e le restrizioni per le parole chiave personalizzate che usano Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356605"
---
# <a name="what-is-a-keyword"></a>Che cos'è una parola chiave?

Una parola chiave è una parola o una frase breve che consente di attivare la voce del prodotto. Ad esempio, "Hey Cortana" è la parola chiave per Cortana Assistant. L'attivazione vocale consente agli utenti di iniziare a interagire con il prodotto senza alcuna pratica, semplicemente pronunciando la parola chiave. Poiché il prodotto è costantemente in ascolto per la parola chiave, tutto l'audio viene elaborato localmente sul dispositivo dell'utente fino a quando non si verifica un rilevamento per assicurarsi che i dati rimangano il più possibile privati.

## <a name="core-features-of-custom-keyword"></a>Funzionalità principali della parola chiave Custom

Con le funzionalità di personalizzazione, prestazioni e integrazione delle parole chiave personalizzate, è possibile personalizzare l'attivazione vocale per soddisfare al meglio la visione del prodotto e le esigenze degli utenti.

| Funzionalità | Descrizione |
|----------|----------|
| Personalizzazione delle parole chiave | Come estensione del marchio, una parola chiave rinforza l'equità creata con i clienti. Il portale di parole chiave personalizzato in speech studio consente di specificare qualsiasi parola o breve frase che meglio rappresenta il marchio. È possibile personalizzare ulteriormente la parola chiave scegliendo le pronunce corrette, che verranno rispettate dal modello di parola chiave generato.
| Verifica parole chiave | Quando la parola chiave viene rilevata localmente in modo sicuro, l'audio viene inviato al cloud per un'ulteriore verifica che un utente abbia detto la parola chiave. La verifica delle parole chiave offre un ulteriore livello di sicurezza, riducendo l'effetto di un rilevamento locale errato e la protezione della privacy degli utenti.
| Assistente vocale & l'integrazione dell'SDK vocale | Le parole chiave generate dalla parola chiave Custom in speech studio possono essere integrate facilmente all'interno del dispositivo o dell'applicazione tramite l'SDK di riconoscimento vocale. È sufficiente puntare l'SDK al modello di parola chiave fornito da speech studio e il prodotto sarà attivato tramite Voice, supportato dalla verifica delle parole chiave. Per completare l'esperienza vocale del prodotto, è possibile creare un proprio [Assistente vocale](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Introduzione alle parole chiave personalizzate

* Vedere [nozioni fondamentali sulle parole chiave personalizzate](custom-keyword-basics.md) per i modelli di utilizzo e progettazione di base.
* Come [attivare l'attivazione vocale del prodotto con l'SDK di riconoscimento vocale usando C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Scegliere una parola chiave valida

La creazione di una parola chiave efficace è essenziale per garantire che il dispositivo risponda in modo coerente e accurato. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione. Quando si sceglie una parola chiave, tenere presenti le linee guida seguenti:

> [!div class="checklist"]
> * La parola chiave deve essere una parola o una frase in inglese.
> * Per pronunciarla, non devono servire più di due secondi.
> * Le parole composte da 4-7 sillabe sono ottimali. Ad esempio, "Hey, computer" è una parola chiave corretta. "Ehi" è una parola inappropriata.
> * Le parole chiave devono seguire le regole comuni di pronuncia della lingua inglese.
> * Una parola particolare o addirittura inventata che segue le normali regole di pronuncia italiana potrebbe ridurre i falsi positivi. Ad esempio, "computerama" potrebbe essere una parola chiave corretta.
> * Non scegliere una parola comune. Ad esempio, "mangiare" e "andare" sono parole che le persone ripetono spesso parlando normalmente e potrebbero essere falsi trigger per il dispositivo.
> * Evitare di usare una parola chiave che potrebbe avere pronunce alternative. Gli utenti dovranno conoscere la pronuncia "corretta" per fare in modo che il dispositivo risponda. Ad esempio, "509" può essere pronunciato "cinquecentonove", "cinque zero nove" o "cinquecento nove". "U.F.O." può essere pronunciato "U-F-O" oppure "ufo". "Ancora" può essere pronunciata "àncora" o "ancóra".
> * Non usare caratteri speciali, simboli o cifre. Ad esempio, "go #" e "20 + Cats" potrebbero essere parole chiave problematiche. mentre "vai euro" o "venti più gatti" possono andare bene. È comunque possibile usare i simboli nella personalizzazione e usare il marketing e la documentazione per ribadire la pronuncia corretta.

> [!NOTE]
> Se si sceglie una parola con marchio come parola chiave, assicurarsi di essere proprietari del marchio o di avere l'autorizzazione del proprietario del marchio a usare la parola. Microsoft non è responsabile di eventuali problemi legali che potrebbero derivare dalla scelta della parola chiave.

## <a name="see-samples-on-github"></a>Vedere gli esempi in GitHub

* [Riconoscere parole chiave con l'SDK di riconoscimento vocale, su piattaforma UWP (Universal Windows Platform) con C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Riconoscere parole chiave con l'SDK di riconoscimento vocale su Android con Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
* [Ottenere Speech SDK](speech-sdk.md)
* [Altre informazioni sugli assistenti vocali](voice-assistants.md)
