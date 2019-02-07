---
title: Informazioni sul servizio Traduzione vocale
titleSuffix: Azure Cognitive Services
description: Usare l'API Traduzione vocale per aggiungere la traduzione vocale e la traduzione con riconoscimento vocale alle applicazioni in uso.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 1983ecbdee3d15efc73ad395fd07cbc7cd4f279c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769726"
---
# <a name="what-is-translator-speech-api"></a>Informazioni sull'API Traduzione vocale

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

L'API Traduzione vocale può essere usata per aggiungere traduzioni vocali end-to-end e in tempo reale ad applicazioni, strumenti o a qualsiasi altra soluzione in cui sia necessaria la traduzione vocale in più lingue, indipendentemente dal sistema operativo di destinazione o dal linguaggio di sviluppo. L'API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale.

L'API Traduzione testuale è un servizio di Azure, parte della [raccolta delle API dei Servizi cognitivi di Azure](https://docs.microsoft.com/azure/) di algoritmi di apprendimento automatico e algoritmi AI nel cloud, facilmente utilizzabile nei progetti di sviluppo.

Con l'API Traduzione vocale le applicazioni client trasmettono audio vocale al servizio e ricevono un flusso di risultati basati su testo e audio, inclusi il testo riconosciuto nella lingua di origine e la sua traduzione nella lingua di destinazione. I risultati del testo vengono prodotti applicando il riconoscimento vocale automatico (ASR) alimentato da reti neurali profonde al flusso audio in ingresso. L'output ASR non elaborato viene migliorato ulteriormente con una nuova tecnica chiamata TrueText, per riflettere più da vicino le finalità dell'utente. Ad esempio, TrueText rimuove le disfluenze (gli hmmm e i colpi di tosse), le parole ripetute e ripristina la punteggiatura e l'uso delle maiuscole corretti. Vi è inoltre la possibilità di nascondere o escludere i contenuti volgari. Per i motori di riconoscimento e di traduzione il training viene eseguito specificamente per gestire gli argomenti delle conversazioni. 

Il servizio Traduzione vocale usa il rilevamento dei silenzi per determinare la fine di un'espressione. Dopo una pausa nell'attività vocale, il servizio riprodurrà un risultato finale per l'espressione completata. Il servizio può inoltre restituire risultati parziali, che forniscono traduzioni e riconoscimenti intermedi per un'espressione in corso. 

Per la traduzione vocale, il servizio offre la possibilità di sintetizzare il vocale (sintesi vocale) dal testo parlato alle lingue di destinazione. L'audio della sintesi vocale viene creato nel formato specificato dal client. Sono disponibili i formati WAV e MP3.

L'API Traduzione vocale usa il protocollo WebSocket per fornire un canale di comunicazione full-duplex tra il client e il server. 

## <a name="about-microsoft-translator"></a>Informazioni su Microsoft Translator
Microsoft Translator è un servizio di traduzione automatica basato sul cloud. Al centro di questo servizio vi sono l'[API Traduzione testuale](https://www.microsoft.com/en-us/translator/translatorapi.aspx) e l'API Traduzione vocale che sono alla base di vari prodotti e servizi Microsoft e vengono usate da migliaia di aziende in tutto il mondo nelle applicazioni e nei flussi di lavoro, consentendo ai loro contenuti di raggiungere un pubblico mondiale.

Per altre informazioni, vedere il [servizio Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Traduzione automatica neurale (NMT) di Microsoft Translator
Per fornire traduzioni, l'API Traduzione vocale usa sia la tecnologia di traduzione automatica statistica (SMT, Statistical Machine Translation) legacy sia la nuova tecnologia di traduzione automatica neurale (NMT, Neural Machine Translation).

La traduzione automatica basata su statistiche si è stabilizzata in termini di miglioramento delle prestazioni. La qualità della traduzione non migliora più in modo significativo per i sistemi generici con la traduzione automatica statistica. Una nuova tecnologia di traduzione basata sull'intelligenza artificiale sta prendendo piede grazie alle reti neurali.

La traduzione automatica neurale fornisce traduzioni migliori non solo dal punto di vista della qualità della traduzione non elaborata, ma anche perché suonano più fluide, più umane di quelle della traduzione automatica statistica. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole. La traduzione automatica statistica accetta solo il contesto immediato di alcune parole prima e dopo ogni parola.

I modelli della traduzione automatica neurale sono alla base dell'API e non sono visibili agli utenti finali. Le uniche differenze evidenti sono:
* La migliore qualità della traduzione, specialmente per lingue quali il cinese, il giapponese e l'arabo
* L'incompatibilità con le funzionalità di personalizzazione Hub esistenti (per l'utilizzo con l'API Traduzione testuale di Microsoft Translator)

Tutte le lingue supportate per la traduzione vocale sono basate sulla traduzione automatica neurale. Di conseguenza, tutte le traduzioni vocali usano la traduzione automatica neurale. 

La traduzione con riconoscimento vocale può usare una combinazione di traduzione automatica neurale e traduzione automatica statistica a seconda della coppia di lingue. Se la lingua di destinazione è supportata dalla traduzione automatica neurale, la traduzione completa è basata sulla traduzione automatica neurale. Se la lingua di destinazione non è supportata dalla traduzione automatica neurale, la traduzione è un ibrido di traduzione automatica neurale e traduzione automatica statistica che usa la lingua inglese come "perno" tra le due lingue. 

Visualizzare le lingue supportate su [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Altre informazioni sul [funzionamento della traduzione automatica neurale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Iscrizione](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Iniziare a scrivere codice](quickstarts/csharp.md)

## <a name="see-also"></a>Vedere anche 
- [Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/)
- [Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
- [Informazioni sui prezzi e sulla soluzione](https://www.microsoft.com/en-us/translator/home.aspx) 
