---
title: Informazioni sul riconoscimento vocale
description: Panoramica delle funzionalità dell'API Riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: 5a19366f6aeec2422ec8671ba3c32f642b5e01ac
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361437"
---
# <a name="about-the-speech-to-text-api"></a>Informazioni sull'API Riconoscimento vocale

L'API **Riconoscimento vocale** *trascrive* i flussi audio in testo che l'applicazione può mostrare all'utente o che può fungere da input per i comandi. Le API possono essere usate con una libreria client SDK (per le piattaforme e le lingue supportate) o con un'API REST.

L'API **Riconoscimento vocale** offre le funzionalità seguenti:

- Tecnologia avanzata di riconoscimento vocale Microsoft, la stessa usata da Cortana, Office e altri prodotti Microsoft.

- Riconoscimento continuo in tempo reale. Il **riconoscimento vocale** consente agli utenti di trascrivere l'audio in testo in tempo reale. Supporta anche la ricezione dei risultati intermedi delle parole che sono state riconosciute fino a quel momento. Il servizio riconosce automaticamente la fine del parlato. Gli utenti possono anche scegliere opzioni di formattazione aggiuntive, inclusi l'uso di lettere maiuscole e minuscole e della punteggiatura, il mascheramento di contenuto volgare e la normalizzazione del testo inversa.

- I risultati restituiti in forma lessicale e visualizzata (per ottenere risultati lessicali, vedere DetailedSpeechRecognitionResult negli esempi o l'API).

- Supporto per molte lingue parlate e dialetti. Per l'elenco completo delle lingue supportate in ogni modalità di riconoscimento, vedere [Lingue supportate](language-support.md#speech-to-text).

- Modelli linguistici e acustici personalizzati, per adattare l'applicazione al vocabolario specializzato del dominio, all'ambiente linguistico e al modo di parlare degli utenti.

- Comprensione del linguaggio naturale. Grazie all'integrazione con [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), è possibile capire dal parlato le finalità e le entità. Gli utenti non devono conoscere il vocabolario dell'app, ma possono descrivere ciò che vogliono con parole proprie.

- Se si specifica un output dettagliato per l'oggetto di configurazione riconoscimento vocale (proprietà SpeechConfig.OutputFormat), il servizio restituisce un punteggio di attendibilità. È quindi possibile usare il metodo Best() sul risultato oppure ottenere direttamente il punteggio dall'output JSON restituito dal servizio (simile a result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>Funzionalità API

Alcune delle funzionalità dell'API **Riconoscimento vocale**, soprattutto per quanto riguarda la personalizzazione, sono disponibili tramite REST. La tabella seguente riepiloga le funzionalità di ogni metodo di accesso all'API. Per un elenco completo di funzionalità e i dettagli dell'API, vedere le [informazioni di riferimento su Swagger](https://westus.cris.ai/swagger/ui/index).

| Caso d'uso | REST | SDK |
|-----|-----|-----|----|
| Trascrivere una breve espressione, ad esempio un comando (lunghezza < 15 s), senza risultati temporanei | Yes | Yes |
| Trascrivere un'espressione più lunga (> 15 s) | No  | Yes |
| Trascrivere lo streaming di audio con risultati temporanei facoltativi | No  | Yes |
| Comprendere le finalità di chi parla tramite LUIS | No\* | Yes |
| Creare test di accuratezza | Yes | No  |
| Caricare set di dati per l'adattamento del modello | Yes | No  |
| Creare e gestire modelli di conversione voce/testo | Yes | No  |
| Creare e gestire distribuzioni dei modelli | Yes | No  |
| Gestisci sottoscrizioni | Yes | No  |
| Creare e gestire distribuzioni dei modelli | Yes | No  |
| Creare e gestire distribuzioni dei modelli | Yes | No  |

> [!NOTE]
> L'API REST implementa la limitazione delle richieste dell'API a 25 ogni 5 secondi. Le intestazioni dei messaggi informeranno dei limiti

\* *Le finalità e le entità LUIS possono essere derivate con una sottoscrizione di LUIS separata. Con questa sottoscrizione, l'SDK può chiamare LUIS automaticamente e fornire i risultati relativi a entità e finalità, oltre alle trascrizioni vocali. Con l'API REST, è possibile chiamare LUIS manualmente per derivare finalità ed entità con la sottoscrizione di LUIS.*

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Guida introduttiva: Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
* [Vedere come riconoscere le finalità dai contenuti vocali in C#](how-to-recognize-intents-from-speech-csharp.md)
