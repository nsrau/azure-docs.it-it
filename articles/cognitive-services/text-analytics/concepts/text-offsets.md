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
ms.openlocfilehash: 14fd7c2b034077d818d1a1224d3c4c12a7fc07bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855636"
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

## <a name="offsets-in-api-version-31-preview"></a>Offset nella versione API 3,1-Preview

A partire dalla versione API 3,1-Preview. 1, tutti gli endpoint API Analisi del testo che restituiscono un offset supporteranno il `stringIndexType` parametro. Questo parametro imposta gli `offset` attributi e `length` nell'output dell'API in modo che corrispondano allo schema di iterazione stringa richiesto. Attualmente sono supportati tre tipi:

1. `textElement_v8`(impostazione predefinita): esegue l'iterazione su grafemi come definito dallo standard [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)
2. `unicodeCodePoint`: scorre i [punti di codice Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), lo schema predefinito per Python 3
3. `utf16CodeUnit`: scorre le [unità di codice UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), lo schema predefinito per JavaScript, Java e .NET

Se l'oggetto `stringIndexType` richiesto corrisponde all'ambiente di programmazione desiderato, l'estrazione della sottostringa può essere eseguita usando i metodi della sottostringa o della sezione standard. 

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
