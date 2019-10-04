---
title: Eseguire la migrazione dall'API Traduzione vocale al Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione delle applicazioni dall'API Traduzione vocale al Servizio di riconoscimento vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559573"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Eseguire la migrazione dall'API Traduzione vocale al Servizio di riconoscimento vocale

Usare le informazioni di questo articolo per eseguire la migrazione delle applicazioni dall'API Traduzione vocale Microsoft al [Servizio di riconoscimento vocale](index.yml). Questa guida descrive le differenze tra l'API Traduzione vocale e il Servizio di riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni.

> [!NOTE]
> La chiave di sottoscrizione dell'API Traduzione vocale non verrà accettata dal Servizio di riconoscimento vocale. È necessario creare una nuova sottoscrizione di servizi vocali.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

| Funzionalità                                           | API Traduzione vocale                                  | Servizi Voce | Dettagli                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduzione in testo                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzione in voce                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Endpoint globale                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | I servizi di riconoscimento vocale non offrono un endpoint globale. Un endpoint globale può indirizzare automaticamente il traffico all'endpoint più vicino a livello di area, riducendo la latenza dell'applicazione.                                                    |
| Endpoint a livello di area                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite di tempo della connessione                             | 90 minuti                                               | Senza limiti con l'SDK. 10 minuti con una connessione WebSocket.                                                                                                                                                                                                                                                                                   |
| Chiave di autenticazione nell'intestazione                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Più lingue tradotte in una singola richiesta | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK disponibili                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Vedere la [documentazione di servizi vocali](index.yml) per gli SDK disponibili.                                                                                                                                                    |
| Connessioni WebSocket                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API di lingue                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | I servizi di riconoscimento vocale supportano la stessa gamma di linguaggi descritti nell'articolo di [riferimento sulle lingue dell'API di traduzione](../translator-speech/languages-reference.md) . |
| Marcatore e filtro per contenuto volgare                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM come input                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Altri tipi di file come input                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Risultati parziali                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informazioni di temporizzazione                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID correlazione                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelli conversione voce/testo personalizzati                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | I servizi di riconoscimento vocale offrono modelli di riconoscimento vocale personalizzati che consentono di personalizzare il riconoscimento vocale per il vocabolario univoco dell'organizzazione.                                                                                                                                           |
| Modelli di traduzione personalizzati                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | La sottoscrizione all'API Traduzione testuale Microsoft consente di usare il [traduttore personalizzato](https://www.microsoft.com/translator/business/customization/) per usare i propri dati per traduzioni più accurate.                                                 |

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'organizzazione dispone di applicazioni in fase di sviluppo o in produzione che usano l'API Traduzione vocale, è necessario aggiornarle per l'uso del Servizio di riconoscimento vocale. Per informazioni sugli SDK disponibili, esempi di codice ed esercitazioni, vedere la [documentazione del Servizio di riconoscimento vocale](index.yml). Quando si esegue la migrazione, tenere presente quanto segue:

* I servizi di riconoscimento vocale non offrono un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente.

* Se l'applicazione usa connessioni di lunga durata e non può usare gli SDK disponibili, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti, eseguendo la riconnessione nel momento opportuno.

* Se l'applicazione usa l'API Traduzione testuale e l'API Traduzione vocale per abilitare i modelli di traduzione personalizzati, è possibile aggiungere gli ID di categoria direttamente usando il Servizio di riconoscimento vocale.

* Diversamente dalla API Traduzione vocale, i servizi di riconoscimento vocale possono completare le traduzioni in più linguaggi in un'unica richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Prova gratuitamente i servizi vocali](get-started.md)
* [Avvio rapido: Riconoscere i contenuti vocali in un'app UWP con Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni sul servizio Voce](overview.md)
* [Documentazione di Speech Services e Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
