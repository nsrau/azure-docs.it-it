---
title: Eseguire la migrazione dall'API di riconoscimento vocale del traduttore al servizio di riconoscimento vocaleMigrate from the Translator Speech API to the Speech service
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione delle applicazioni dall'API Traduzione vocale al servizio di riconoscimento vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560899"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Eseguire la migrazione dall'API di riconoscimento vocale del traduttore al servizio di riconoscimento vocaleMigrate from the Translator Speech API to the Speech service

Utilizzare questo articolo per eseguire la migrazione delle applicazioni dall'API Microsoft Translator Speech al [servizio di riconoscimento vocale](index.yml). Questa guida descrive le differenze tra l'API Di scorso intervento tra i traduttori e il servizio di riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni.

> [!NOTE]
> La chiave di sottoscrizione dell'API Traduzione vocale non verrà accettata dal servizio di riconoscimento vocale. È necessario creare una nuova sottoscrizione al servizio di riconoscimento vocale.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

| Funzionalità                                           | API Traduzione vocale                                  | Servizio Voce | Dettagli                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduzione in testo                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzione in voce                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Endpoint globale                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Il servizio di riconoscimento vocale non offre un endpoint globale. Un endpoint globale può indirizzare automaticamente il traffico all'endpoint più vicino a livello di area, riducendo la latenza dell'applicazione.                                                    |
| Endpoint a livello di area                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite di tempo della connessione                             | 90 minuti                                               | Senza limiti con l'SDK. 10 minuti con una connessione WebSocket.                                                                                                                                                                                                                                                                                   |
| Chiave di autenticazione nell'intestazione                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Più lingue tradotte in una singola richiesta | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK disponibili                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Vedere la documentazione del [servizio di riconoscimento vocale](index.yml) per gli SDK disponibili.                                                                                                                                                    |
| Connessioni WebSocket                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API di lingue                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Il servizio di riconoscimento vocale supporta la stessa gamma di lingue descritte nell'articolo di [riferimento sulle lingue dell'API Translator.](../translator-speech/languages-reference.md) |
| Marcatore e filtro per contenuto volgare                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM come input                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Altri tipi di file come input                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Risultati parziali                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informazioni di temporizzazione                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID correlazione                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelli conversione voce/testo personalizzati                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Il servizio di riconoscimento vocale offre modelli vocali personalizzati che consentono di personalizzare il riconoscimento vocale in base al vocabolario univoco dell'organizzazione.                                                                                                                                           |
| Modelli di traduzione personalizzati                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | La sottoscrizione all'API Traduzione testuale Microsoft consente di usare il [traduttore personalizzato](https://www.microsoft.com/translator/business/customization/) per usare i propri dati per traduzioni più accurate.                                                 |

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'utente o l'organizzazione dispone di applicazioni in fase di sviluppo o produzione che utilizzano l'API di riconoscimento vocale del traduttore, è necessario aggiornarle per usare il servizio di riconoscimento vocale. Vedere la documentazione del [servizio di riconoscimento vocale](index.yml) per SDK, esempi di codice ed esercitazioni disponibili. Quando si esegue la migrazione, tenere presente quanto segue:

* Il servizio di riconoscimento vocale non offre un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente.

* Se l'applicazione usa connessioni di lunga durata e non può usare gli SDK disponibili, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

* Se l'applicazione usa l'API Traduzione testo e l'API Di riconoscimento vocale per abilitare modelli di traduzione personalizzati, è possibile aggiungere direttamente gli ID di categoria usando il servizio di riconoscimento vocale.

* A differenza dell'API Traduzione vocale, il servizio di riconoscimento vocale può completare le traduzioni in più lingue in un'unica richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Prova il servizio di riconoscimento vocale gratuitamente](get-started.md)
* [Avvio rapido: Riconoscimento vocale in un'applicazione piattaforma UWP con Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Vedere anche

* [Che cos'è il servizio di riconoscimento vocale](overview.md)
* [Servizio di riconoscimento vocale e documentazione di Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
