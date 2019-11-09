---
title: Personalizzare un modello linguistico in Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo contiene informazioni generali sul modello linguistico in Video Indexer e su come personalizzarlo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838309"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalizzare un modello linguistico con Video Indexer

Video Indexer supporta il riconoscimento vocale automatico tramite l'integrazione con il [Servizio di riconoscimento vocale personalizzato](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/) di Microsoft. È possibile personalizzare il modello linguistico caricando il testo di adattamento, ovvero il testo dal dominio al cui vocabolario si intende adattare il motore. Quando si esegue il training del modello, le nuove parole visualizzate nel testo di adattamento vengono riconosciute, presupponendo una pronuncia predefinita, e il modello linguistico apprenderà nuove sequenze probabili di parole. I modelli di lingua personalizzati sono supportati per inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo, Portoghese brasiliano, Hindi e coreano. 

Prendiamo come esempio una parola estremamente specifica, come "Kubernetes" (nel contesto del servizio Azure Kubernetes). Poiché la parola è nuova in Video Indexer, viene riconosciuta come "community". È necessario eseguire il training del modello affinché la parola venga riconosciuta come "Kubernetes". In altri casi le parole esistono, ma il modello linguistico non prevede che vengano visualizzate in un determinato contesto. "Servizio contenitore", ad esempio, non è una sequenza di 2 parole che un modello linguistico non specializzato riconoscerebbe come un set specifico di parole.

È possibile caricare parole senza contesto in un elenco in un file di testo. Questa personalizzazione viene considerata come parziale. In alternativa, è possibile caricare più file di testo della documentazione o frasi correlate al contenuto per un migliore adattamento.

È possibile usare le API di Video Indexer o il sito Web per creare e modificare modelli linguistici personalizzati, come descritto negli argomenti della sezione [Passaggi successivi](#next-steps) di questo argomento.

## <a name="best-practices-for-custom-language-models"></a>Procedure consigliate per modelli linguistici personalizzati

Video Indexer apprende in base alle probabilità delle combinazioni di parole, pertanto, per un apprendimento migliore, è necessario:

* Indicare un numero sufficiente di esempi concreti di frasi come verrebbero pronunciate.
* Inserire solo una frase per ogni riga, non un numero maggiore. In caso contrario, il sistema apprenderà in base alle probabilità tra le frasi.
* È possibile inserire una parola come una frase per evidenziarla rispetto ad altre, ma il sistema apprende meglio da frasi complete.
* Quando si introducono nuove parole oppure acronimi, è opportuno assegnare molti esempi di uso in una frase completa per dare il maggior contesto possibile al sistema.
* Provare a inserire diverse opzioni di adattamento per scoprirne il funzionamento.
* Evitare la ripetizione della stessa frase esatta più volte perché potrebbe causare distorsione rispetto al resto dell'input.
* Evitare di includere simboli non comuni (~, # @ % &) perché verranno rimossi come le frasi in cui sono presenti.
* Evitare di inserire input di dimensioni eccessive, ad esempio centinaia di migliaia di frasi, perché verrebbe ridotto l'effetto dell'aumento.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md)

[Customize Language model using the website](customize-language-model-with-website.md) (Personalizzare il modello linguistico usando il sito Web)
