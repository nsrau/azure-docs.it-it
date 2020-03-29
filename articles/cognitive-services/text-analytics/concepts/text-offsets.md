---
title: Offset di testo nell'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Scopri gli offset causati dalle codifiche multilingue ed emoji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219236"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Offset di testo nell'output dell'API Di analisi del testo

Il supporto multilingue ed emoji ha portato a codifiche Unicode che utilizzano più di un punto di [codice](https://wikipedia.org/wiki/Code_point) per rappresentare un singolo carattere visualizzato, chiamato un grafema. Ad esempio, emoji come 👍 🌷 e possono utilizzare diversi caratteri per comporre la forma con caratteri aggiuntivi per gli attributi visivi, ad esempio il tono dello skin. Allo stesso modo, la `अनुच्छेद` parola hindi è codificata come cinque lettere e tre segni di combinazione.

A causa delle diverse lunghezze delle possibili codifiche multilingue ed emoji, l'API Analisi del testo può restituire offset nella risposta.

## <a name="offsets-in-the-api-response"></a>Offset nella risposta API. 

Ogni volta che viene restituita una risposta API, ad esempio [Riconoscimento entità denominato](../how-tos/text-analytics-how-to-entity-linking.md) o [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md), tenere presente quanto segue:

* Gli elementi nella risposta possono essere specifici dell'endpoint chiamato. 
* I payload HTTP POST/GET sono codificati in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), che possono essere o meno la codifica dei caratteri predefinita nel compilatore o nel sistema operativo sul lato client.
* Gli offset si riferiscono ai conteggi dei grafemi in base allo standard [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) non ai conteggi dei caratteri.

## <a name="extracting-substrings-from-text-with-offsets"></a>Estrazione di sottostringhe dal testo con offset

Gli offset possono causare problemi quando si utilizzano metodi di sottostringa basati su caratteri, ad esempio il metodo [.NET substring().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Un problema è che un offset può causare un metodo sottostringa terminare nel mezzo di una codifica del grafema multicarattere anziché alla fine.

In .NET è consigliabile usare la classe [StringInfo,](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) che consente di utilizzare una stringa come una serie di elementi testuali anziché singoli oggetti carattere. È inoltre possibile cercare le librerie con separatore di grafemi nell'ambiente software preferito. 

L'API Analisi del testo restituisce anche questi elementi testuali, per comodità.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rileva lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
