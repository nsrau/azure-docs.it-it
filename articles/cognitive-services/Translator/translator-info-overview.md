---
title: Informazioni sull'API Traduzione testuale - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Integrare l'API Traduzione testuale in applicazioni, siti Web, strumenti e altre soluzioni per assicurare esperienze utente multilingue.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 8f27e8e8693fc5d1d3e432b78f2877d71274f146
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978476"
---
# <a name="what-is-the-translator-text-api"></a>Informazioni sull'API Traduzione testuale

L'API Traduzione testuale è facile da integrare in applicazioni, siti Web, strumenti e soluzioni. Consente di aggiungere esperienze utente multilingue in [oltre 60 lingue](languages.md) e può essere usata in qualsiasi piattaforma hardware con qualsiasi sistema operativo per la traduzione da testo a testo in una lingua.

L'API Traduzione testuale fa parte della raccolta [API Servizi cognitivi](https://docs.microsoft.com/azure/#pivot=products&panel=ai) di Azure di algoritmi di apprendimento automatico e algoritmi AI nel cloud ed è facilmente utilizzabile nei progetti di sviluppo.

## <a name="about-microsoft-translator"></a>Informazioni su Microsoft Translator

Microsoft Translator è un servizio di traduzione automatica basato sul cloud. Il servizio di base è l'API Traduzione testuale che supporta vari prodotti e servizi Microsoft e viene usato da migliaia di aziende in tutto il mondo nelle applicazioni e nei flussi di lavoro, consentendo ai loro contenuti di raggiungere un pubblico globale.

Traduzione vocale, basato sull'API Traduzione testuale, è disponibile anche tramite il [servizio Voce Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Combina le funzionalità dell'API Traduzione vocale e del Servizio riconoscimento vocale personalizzato (anteprima) in un unico servizio completamente personalizzabile. Il servizio Voce sta sostituendo l'API Traduzione vocale, che verrà ritirato il 15 ottobre 2019.

## <a name="language-support"></a>Supporto per le lingue

Microsoft Translator fornisce supporto multilingue per la traduzione, la traslitterazione, il rilevamento delle lingue e i dizionari. Per un elenco completo, vedere il [supporto per le lingue](language-support.md) o accedere all'elenco a livello di codice con l'[API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Traduzione automatica neurale di Microsoft Translator

La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di qualità elevata basate sull'intelligenza artificiale. Sostituisce la tecnologia di traduzione automatica statistica legacy che ha raggiunto uno stato stazionario di qualità a metà del 2010.

La traduzione automatica neurale fornisce traduzioni migliori rispetto alla traduzione automatica statistica non solo dal punto di vista della qualità della traduzione non elaborata, ma anche perché suonano più fluide e umane. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole. La traduzione automatica statistica accetta solo il contesto immediato di alcune parole prima e dopo ogni parola.

I modelli della traduzione automatica neurale sono alla base dell'API e non sono visibili agli utenti finali. L'unica differenza più evidente è la migliore qualità della traduzione, specialmente per lingue quali il cinese, il giapponese e l'arabo.

Altre informazioni sul [funzionamento della traduzione automatica neurale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Personalizzazione della lingua

Custom Translator, un'estensione del servizio core Microsoft Translator, può essere usato insieme all'API Traduzione testuale per consentire di personalizzare il sistema di traduzione neurale e di migliorare la traduzione per la terminologia e lo stile specifici.

Con Custom Translator è possibile creare sistemi di traduzione che gestiscono la terminologia usata per la propria attività o azienda. Il sistema di traduzione personalizzata verrà quindi integrato facilmente in applicazioni, flussi di lavoro e siti Web esistenti, in più tipi di dispositivi, attraverso la normale API Traduzione testuale Microsoft usando il parametro di categoria.

Altre informazioni sulla [personalizzazione della lingua](customization.md)

## <a name="next-steps"></a>Passaggi successivi

- [Iscriversi](translator-text-how-to-signup.md) per una chiave di accesso.
- La [documentazione di riferimento delle API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) offre la documentazione tecnica per le API.
- [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
