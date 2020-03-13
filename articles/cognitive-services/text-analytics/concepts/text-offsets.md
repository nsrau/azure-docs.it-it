---
title: Offset di testo nel API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Informazioni sugli offset causati da codifiche multilingue e emoji.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219236"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Offset di testo nell'output API Analisi del testo

Il supporto multilingue e emoji ha portato a codifiche Unicode che usano più di un [punto di codice](https://wikipedia.org/wiki/Code_point) per rappresentare un singolo carattere visualizzato, denominato grafema. Ad esempio, emoji come 🌷 e 👍 possono usare diversi caratteri per comporre la forma con caratteri aggiuntivi per gli attributi visivi, ad esempio il tono della pelle. Analogamente, la parola Hindi `अनुच्छेद` è codificata come cinque lettere e tre segni di combinazione.

A causa delle diverse lunghezze delle codifiche multilingue e emoji possibili, il API Analisi del testo può restituire offset nella risposta.

## <a name="offsets-in-the-api-response"></a>Offset nella risposta dell'API. 

Ogni volta che viene restituita la risposta dell'API, ad esempio il [riconoscimento di entità denominate](../how-tos/text-analytics-how-to-entity-linking.md) o [analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md), tenere presente quanto segue:

* Gli elementi nella risposta possono essere specifici dell'endpoint chiamato. 
* I payload HTTP POST/GET sono codificati in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), che può essere o meno la codifica dei caratteri predefinita nel compilatore o nel sistema operativo sul lato client.
* Gli offset fanno riferimento ai conteggi di grafema in base allo standard [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , non ai conteggi dei caratteri.

## <a name="extracting-substrings-from-text-with-offsets"></a>Estrazione di sottostringhe da testo con offset

Gli offset possono causare problemi quando si usano metodi di sottostringa basate su caratteri, ad esempio il metodo [substring ()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) .NET. Un problema è che un offset può causare la fine di un metodo di sottostringa al centro di una codifica grafema multicarattere anziché alla fine.

In .NET è consigliabile utilizzare la classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , che consente di utilizzare una stringa come una serie di elementi testuali, anziché singoli oggetti character. È anche possibile cercare librerie Splitter grafema nell'ambiente software preferito. 

Il API Analisi del testo restituisce anche questi elementi testuali per praticità.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
