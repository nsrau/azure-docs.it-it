---
title: Informazioni sull'API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Integrare l'API Traduzione testuale in applicazioni, siti Web, strumenti e altre soluzioni per assicurare esperienze utente multilingue.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: erhopf
ms.openlocfilehash: 6c89ff41531b130843eb288b98ffe7def1d8915e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645045"
---
# <a name="what-is-translator-text-api"></a>Informazioni sull'API Traduzione testuale

L'API Traduzione testuale può essere facilmente integrata in applicazioni, siti Web, strumenti o altre soluzioni per assicurare esperienze utente multilingue in [oltre 60 lingue](languages.md). Può essere usata in qualsiasi piattaforma hardware e con qualsiasi sistema operativo per eseguire la traduzione da testo a testo in una lingua.

L'API Traduzione testuale fa parte della raccolta delle [API dei Servizi cognitivi di Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) di algoritmi di apprendimento automatico e algoritmi AI nel cloud, facilmente utilizzabile nei progetti di sviluppo.

## <a name="about-microsoft-translator"></a>Informazioni su Microsoft Translator

Microsoft Translator è un servizio di traduzione automatica basato sul cloud. Al centro di questo servizio vi sono l'API Traduzione testuale e l'[API Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation) che sono alla base di vari prodotti e servizi Microsoft e vengono usate da migliaia di aziende in tutto il mondo nelle applicazioni e nei flussi di lavoro, consentendo ai loro contenuti di raggiungere un pubblico mondiale.

La traduzione vocale è disponibile anche tramite l'[anteprima di Riconoscimento vocale di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/speech-service/), che combina l'API Traduzione vocale, l'API Riconoscimento vocale Bing e il Servizio di riconoscimento vocale personalizzato (anteprima) in un servizio unificato e completamente personalizzabile.  

Per altre informazioni, vedere il [servizio Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>Personalizzazione della lingua

Custom Translator, un'estensione del servizio core Microsoft Translator, può essere usato insieme all'API Traduzione testuale per consentire di personalizzare il sistema di traduzione neurale e di migliorare la traduzione per la terminologia e lo stile specifici.

Con Custom Translator è possibile creare sistemi di traduzione che gestiscono la terminologia usata per la propria attività o azienda. Il sistema di traduzione personalizzata verrà quindi integrato facilmente in applicazioni, flussi di lavoro e siti Web esistenti, in più tipi di dispositivi, attraverso la normale API Traduzione testuale Microsoft usando il parametro di categoria.

Altre informazioni sulla [personalizzazione della lingua](customization.md)

## <a name="microsoft-translator-neural-machine-translation"></a>Traduzione automatica neurale di Microsoft Translator

La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di qualità elevata basate sull'intelligenza artificiale. Sostituisce la tecnologia di traduzione automatica statistica legacy che ha raggiunto uno stato stazionario di qualità a metà del 2010.

La traduzione automatica neurale fornisce traduzioni migliori rispetto alla traduzione automatica statistica non solo dal punto di vista della qualità della traduzione non elaborata, ma anche perché suonano più fluide e umane. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole. La traduzione automatica statistica accetta solo il contesto immediato di alcune parole prima e dopo ogni parola.

I modelli della traduzione automatica neurale sono alla base dell'API e non sono visibili agli utenti finali. L'unica differenza più evidente è la migliore qualità della traduzione, specialmente per lingue quali il cinese, il giapponese e l'arabo.

Altre informazioni sul [funzionamento della traduzione automatica neurale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dettagli prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Iscriversi](translator-text-how-to-signup.md) per una chiave di accesso.

- L'[Avvio rapido](quickstarts/csharp.md) è una procedura dettagliata per le chiamate API REST scritte in C#. Leggere le informazioni su come tradurre il testo da una lingua a un'altra con una quantità minima di codice.

- La [documentazione di riferimento dell'API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) offre la documentazione tecnica per le API.

## <a name="see-also"></a>Vedere anche 

- [Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
- [Informazioni sui prezzi e sulla soluzione](https://www.microsoft.com/en-us/translator/default.aspx)
