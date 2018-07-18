---
title: Informazioni sul riconoscimento vocale | Microsoft Docs
description: Panoramica delle funzionalità dell'API Riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378452"
---
# <a name="about-the-speech-to-text-api"></a>Informazioni sull'API Riconoscimento vocale

L'API **Riconoscimento vocale** *trascrive* i flussi audio in testo che l'applicazione può mostrare all'utente o che può fungere da input per i comandi. Le API possono essere usate con una libreria client SDK (per le piattaforme e le lingue supportate) o con un'API REST.

L'API **Riconoscimento vocale** offre le funzionalità seguenti:

- Tecnologia avanzata di riconoscimento vocale Microsoft, la stessa usata da Cortana, Office e altri prodotti Microsoft.

- Riconoscimento continuo in tempo reale. Il **riconoscimento vocale** consente agli utenti di trascrivere l'audio in testo in tempo reale. Supporta anche la ricezione dei risultati intermedi delle parole che sono state riconosciute fino a quel momento. Il servizio riconosce automaticamente la fine del parlato. Gli utenti possono anche scegliere opzioni di formattazione aggiuntive, inclusi l'uso di lettere maiuscole e minuscole e della punteggiatura, il mascheramento di contenuto volgare e la normalizzazione del testo.

- Risultati ottimizzati di **riconoscimento vocale** per scenari interattivi, di conversazione e di dettatura. 

- Supporto per molte lingue parlate in più dialetti. Per l'elenco completo delle lingue supportate in ogni modalità di riconoscimento, vedere [Lingue supportate](supported-languages.md#speech-to-text).

- Modelli linguistici e acustici personalizzati, per adattare l'applicazione al modo di parlare, all'ambiente linguistico e al vocabolario personale degli utenti.

- Comprensione del linguaggio naturale. Grazie all'integrazione con [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), è possibile capire dal parlato le finalità e le entità. Gli utenti non devono conoscere il vocabolario dell'app, ma possono descrivere ciò che vogliono con parole proprie.

## <a name="api-capabilities"></a>Funzionalità API

Alcune funzionalità dell'API **Riconoscimento vocale** non sono disponibili tramite REST. La tabella seguente riepiloga le funzionalità di ogni metodo di accesso all'API.

| Caso d'uso | REST | SDK |
|-----|-----|-----|----|
| Trascrivere una breve espressione, ad esempio un comando (lunghezza < 15 s), senza risultati temporanei | Sì | Sì |
| Trascrivere un'espressione più lunga (> 15 s) | No  | Sì |
| Trascrivere lo streaming di audio con risultati temporanei facoltativi | No  | Sì |
| Comprendere le finalità di chi parla tramite LUIS | No\* | Sì |

\* *Le finalità e le entità LUIS possono essere derivate con una sottoscrizione di LUIS separata. Con questa sottoscrizione, l'SDK può chiamare LUIS automaticamente e fornire i risultati relativi a entità e finalità, oltre alle trascrizioni vocali. Con l'API REST, è possibile chiamare LUIS manualmente per derivare finalità ed entità con la sottoscrizione di LUIS.*

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-windows.md)
