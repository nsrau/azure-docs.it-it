---
title: API Riconoscimento vocale Bing di Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare Microsoft Speech API per aggiungere alle app azioni basate su contenuto vocale, inclusa l'interazione in tempo reale con gli utenti.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 960d2d402f223b306aa6ff05b567d13525e3d525
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340359"
---
# <a name="what-is-bing-speech"></a>Informazioni su Riconoscimento vocale Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

API Riconoscimento vocale Bing di Microsoft è un'API basata sul cloud che offre agli sviluppatori un modo semplice per creare funzioni vocali avanzate nelle applicazioni, come comandi vocali, dialoghi con conversazioni naturali, trascrizione del parlato e dettatura. Microsoft Speech API supporta sia il *riconoscimento vocale* sia la *sintesi vocale*.

- L'API **Riconoscimento vocale** converte il parlato in testo che può essere usato come input o come comandi per controllare l'applicazione.
- L'API **Sintesi vocale** converte il testo in flussi audio che possono essere riprodotti agli utenti dell'applicazione.

## <a name="speech-to-text-speech-recognition"></a>Riconoscimento vocale

L'API Riconoscimento vocale Microsoft *trascrive* i flussi audio in testo che l'applicazione può mostrare all'utente o che può fungere da input per i comandi. L'API consente agli sviluppatori di aggiungere contenuto vocale alle proprie app in due modi diversi: tramite le API REST **o** le librerie client basate su WebSocket.

- [API REST](GetStarted/GetStartedREST.md): gli sviluppatori possono usare chiamate HTTP dalle proprie app al servizio per il riconoscimento vocale.
- [Librerie client](GetStarted/GetStartedClientLibraries.md): per le funzionalità avanzate, gli sviluppatori possono scaricare le librerie client del riconoscimento vocale Microsoft e collegarle alle proprie app.  Le librerie client sono disponibili su diverse piattaforme (Windows, Android, iOS) con linguaggi diversi (C#, Java, JavaScript, ObjectiveC). A differenza delle API REST, le librerie client usano un protocollo basato su WebSocket.

| Casi d'uso | [API REST](GetStarted/GetStartedREST.md) | [Librerie client](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Convertire breve contenuto vocale, ad esempio comandi (lunghezza audio < 15 secondi) senza risultati temporanei | Yes | Yes |
| Convertire contenuto audio lungo (> 15 secondi) | No  | Yes |
| Trasmettere un flusso audio con risultati temporanei desiderati | No  | Yes |
| Comprendere il testo convertito dall'audio tramite LUIS | No  | Yes |

Indipendentemente dall'approccio scelto dagli sviluppatori (API REST o librerie client), il Servizio di riconoscimento vocale Microsoft supporta le funzionalità seguenti:

- Tecnologie avanzate di riconoscimento vocale Microsoft, usate da Cortana, Office Dictation, Office Translator e altri prodotti Microsoft.
- Riconoscimento continuo in tempo reale. L'API Riconoscimento vocale consente agli utenti di trascrivere l'audio in testo in tempo reale e supporta la ricezione dei risultati intermedi relativi alle parole riconosciute fino a quel momento. Il Servizio di riconoscimento vocale supporta anche il rilevamento della fine del parlato. Gli utenti possono anche scegliere funzionalità di formattazione aggiuntive, come l'uso di lettere maiuscole e minuscole e della punteggiatura, il mascheramento di contenuto volgare e la normalizzazione del testo.
- Supporta risultati ottimizzati di riconoscimento vocale per scenari *interattivi*, di *conversazione* e di *dettatura*. Per gli scenari utente che richiedono modelli di lingua personalizzati e modelli acustici, il [Servizio di riconoscimento vocale personalizzato](../custom-speech-service/cognitive-services-custom-speech-home.md) consente di creare modelli di riconoscimento vocale personalizzati per l'applicazione e per gli utenti.
- Supporta molte lingue parlate in più dialetti. Per l'elenco completo delle lingue supportate in ogni modalità di riconoscimento, vedere l'articolo relativo alle [lingue di riconoscimento](api-reference-rest/supportedlanguages.md).
- Integrazione con Language Understanding. Oltre a convertire l'input audio in testo, il *riconoscimento vocale* offre alle applicazioni una funzionalità aggiuntiva per comprendere il significato del testo. Usa il servizio [Language Understanding Intelligent Services (LUIS)](../LUIS/what-is-luis.md) per estrarre finalità ed entità dal testo riconosciuto.

### <a name="next-steps"></a>Passaggi successivi

- Leggere l'introduzione all'uso del Servizio di riconoscimento vocale Microsoft con le [API REST](GetStarted/GetStartedREST.md) o le [librerie client](GetStarted/GetStartedClientLibraries.md).
- Vedere le [applicazioni di esempio](samples.md) nel linguaggio di programmazione preferito.
- Vedere le informazioni di riferimento sul [protocollo del Servizio di riconoscimento vocale Microsoft](API-Reference-REST/websocketprotocol.md) e sulle API.

## <a name="text-to-speech-speech-synthesis"></a>Sintesi vocale

Le API *Sintesi vocale* usano REST per convertire un testo strutturato in un flusso audio. Le API garantiscono una veloce conversione con sintesi vocale in varie voci e lingue. Gli utenti hanno anche la possibilità di modificare le caratteristiche audio, ad esempio pronuncia, volume e tonalità, tramite tag SSML.

### <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione all'uso del servizio di sintesi vocale Microsoft, vedere le [informazioni di riferimento sull'API Sintesi vocale](api-reference-rest/bingvoiceoutput.md). Per l'elenco completo delle lingue e delle voci supportate dal servizio di sintesi vocale, vedere [Impostazioni locali e caratteri voce supportati](api-reference-rest/bingvoiceoutput.md#SupLocales).
