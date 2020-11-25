---
title: Servizio Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Integrare Traduttore in applicazioni, siti Web, strumenti e altre soluzioni per offrire esperienze utente multilingue.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
ms.custom: cog-serv-seo-aug-2020
keywords: traduttore, traduzione testuale, traduzione automatica, servizio di traduzione
ms.openlocfilehash: 8016c79cbe7f78bdaa18274f8873548e3982df2f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016473"
---
# <a name="what-is-the-translator-service"></a>Che cos'è il servizio Translator?

Translator è un servizio di traduzione automatica basato sul cloud e fa parte della famiglia di API cognitive di [Servizi cognitivi di Azure](../../index.yml?panel=ai&pivot=products) usate per lo sviluppo di app intelligenti. Traduttore è facilmente integrabile in applicazioni, siti Web, strumenti e soluzioni. Consente di aggiungere esperienze utente multilingue in [oltre 70 lingue](./language-support.md) e si può usare in qualsiasi piattaforma hardware con qualsiasi sistema operativo per la traduzione testuale.

## <a name="about-microsoft-translator"></a>Informazioni su Microsoft Translator

Traduttore è integrato in numerosi prodotti e servizi Microsoft e viene usato da migliaia di aziende di tutto il mondo in applicazioni e flussi di lavoro per distribuire contenuti in grado di raggiungere un pubblico globale.

È anche disponibile il servizio di traduzione vocale, basato su Translator, tramite i [servizi Voce di Azure](../speech-service/index.yml). Combina le funzionalità dell'API Traduzione vocale e del Servizio riconoscimento vocale personalizzato (anteprima) in un unico servizio completamente personalizzabile. 

## <a name="language-support"></a>Lingue supportate

Translator fornisce supporto multilingue per la traduzione testuale, la traslitterazione, il rilevamento delle lingue e i dizionari. Per un elenco completo, vedere il [supporto per le lingue](language-support.md) o accedere all'elenco a livello di codice con l'[API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Traduzione automatica neurale di Microsoft Translator

La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di qualità elevata basate sull'intelligenza artificiale. Sostituisce la tecnologia di traduzione automatica statistica legacy che ha raggiunto uno stato stazionario di qualità a metà del 2010.

La traduzione automatica neurale fornisce traduzioni migliori rispetto alla traduzione automatica statistica non solo dal punto di vista della qualità della traduzione non elaborata, ma anche perché suonano più fluide e umane. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole. La traduzione automatica statistica accetta solo il contesto immediato di alcune parole prima e dopo ogni parola.

I modelli della traduzione automatica neurale sono alla base dell'API e non sono visibili agli utenti finali. L'unica differenza più evidente è la migliore qualità della traduzione, specialmente per lingue quali il cinese, il giapponese e l'arabo.

Altre informazioni sul [funzionamento della traduzione automatica neurale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Migliorare le traduzioni con il traduttore personalizzato

Il traduttore personalizzato, un'estensione del servizio Traduttore, può essere usato insieme a Traduttore per personalizzare il sistema di traduzione neurale e migliorare la traduzione in base a terminologia e stili specifici.

Con Custom Translator è possibile creare sistemi di traduzione che gestiscono la terminologia usata per la propria attività o azienda. Il sistema di traduzione personalizzata verrà quindi integrato facilmente in applicazioni, flussi di lavoro e siti Web esistenti, in più tipi di dispositivi, tramite Translator, usando il parametro di categoria.

Altre informazioni sul [traduttore personalizzato](customization.md).

## <a name="next-steps"></a>Passaggi successivi

- [Iscriversi](./translator-how-to-signup.md) per una chiave di accesso.
- Per chiamare rapidamente il servizio Translator, provare l'[avvio rapido](quickstart-translator.md).
- La [documentazione di riferimento delle API](./reference/v3-0-reference.md) offre la documentazione tecnica per le API.
- [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)