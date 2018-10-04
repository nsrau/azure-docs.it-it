---
title: Informazioni sul riconoscimento vocale
description: Panoramica delle funzionalità dell'API Riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7cb0257a7302221f80bb90c0a6c3446cde07290a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434127"
---
# <a name="about-the-speech-to-text-api"></a>Informazioni sull'API Riconoscimento vocale

L'API **Riconoscimento vocale** *trascrive* i flussi audio in testo che l'applicazione può mostrare all'utente o che può fungere da input per i comandi. Le API possono essere usate con una libreria client SDK (per le piattaforme e le lingue supportate) o con un'API REST.

L'API **Riconoscimento vocale** offre le funzionalità seguenti:

- Tecnologia avanzata di riconoscimento vocale Microsoft, la stessa usata da Cortana, Office e altri prodotti Microsoft.

- Riconoscimento continuo in tempo reale. Il **riconoscimento vocale** consente agli utenti di trascrivere l'audio in testo in tempo reale. Supporta anche la ricezione dei risultati intermedi delle parole che sono state riconosciute fino a quel momento. Il servizio riconosce automaticamente la fine del parlato. Gli utenti possono anche scegliere opzioni di formattazione aggiuntive, inclusi l'uso di lettere maiuscole e minuscole e della punteggiatura, il mascheramento di contenuto volgare e la normalizzazione del testo inversa.

- Risultati ottimizzati di **riconoscimento vocale** per scenari interattivi, di conversazione e di dettatura. I risultati riconosciuti vengono restituiti in forma Vocale e Visiva (per ottenere risultati Vocali, vedere DetailedSpeechRecognitionResult gli esempi o l'API).

- Supporto per molte lingue parlate e dialetti. Per l'elenco completo delle lingue supportate in ogni modalità di riconoscimento, vedere [Lingue supportate](language-support.md#speech-to-text).

- Modelli linguistici e acustici personalizzati, per adattare l'applicazione al vocabolario specializzato del dominio, all'ambiente linguistico e al modo di parlare degli utenti.

- Comprensione del linguaggio naturale. Grazie all'integrazione con [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), è possibile capire dal parlato le finalità e le entità. Gli utenti non devono conoscere il vocabolario dell'app, ma possono descrivere ciò che vogliono con parole proprie.

## <a name="api-capabilities"></a>Funzionalità API

Molte delle funzionalità dell'API di **riconoscimento vocale** - soprattutto per quanto riguarda la personalizzazione - sono disponibili tramite REST. La tabella seguente riepiloga le funzionalità di ogni metodo di accesso all'API. Per un elenco completo di funzionalità e i dettagli dell'API, consultare [Swagger](https://swagger/service/11ed9226-335e-4d08-a623-4547014ba2cc#/)

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
> L'API REST implementa la limitazione delle richieste dell'API a 25 ogni 5 secondi. Gli ascoltatori del messaggio saranno informati dei limiti

\* *Le finalità e le entità LUIS possono essere derivate con una sottoscrizione di LUIS separata. Con questa sottoscrizione, l'SDK può chiamare LUIS automaticamente e fornire i risultati relativi a entità e finalità, oltre alle trascrizioni vocali. Con l'API REST, è possibile chiamare LUIS manualmente per derivare finalità ed entità con la sottoscrizione di LUIS.*

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Guida introduttiva: Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
* [Vedere come riconoscere le finalità dai contenuti vocali in C#](how-to-recognize-intents-from-speech-csharp.md)
