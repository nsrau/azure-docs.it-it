---
title: Limiti dei dati per l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Limitazioni dei dati per l'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: c60adb09da05ba945bcf6ccb55e71c395f064211
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965103"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limiti dei dati e di velocità per l'API Analisi del testo
<a name="data-limits"></a>

Usare questo articolo per informazioni sui limiti di dimensioni e velocità per l'invio dei dati all'API Analisi del testo. 

## <a name="data-limits"></a>Limiti dei dati

> [!NOTE]
> * Se è necessario analizzare documenti di dimensioni maggiori rispetto al limite consentito, è possibile suddividere il testo in blocchi di testo più piccoli prima di inviarli all'API. 
> * Un documento è costituito da una singola stringa di caratteri di testo.  

| Limite | valore |
|------------------------|---------------|
| Dimensioni massime di un singolo documento | 5\.120 caratteri misurati in base a [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Si applica anche ad Analisi del testo per la sanità. |
| Dimensioni massime di un singolo documento (endpoint `/analyze`)  | 125.000 caratteri misurati in base a [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Non si applica ad Analisi del testo per la sanità. |
| Dimensioni massime dell'intera richiesta | 1 MB. Si applica anche ad Analisi del testo per la sanità. |

Il numero massimo di documenti che è possibile inviare in una singola richiesta dipende dalla versione dell'API e dalla funzionalità in uso. L'endpoint `/analyze` rifiuterà l'intera richiesta se qualsiasi documento supera le dimensioni massime (125.000 caratteri)

#### <a name="version-3"></a>[Versione 3](#tab/version-3)

I limiti seguenti si applicano all'API v3 corrente. Il superamento dei limiti indicati di seguito provocherà un codice errore HTTP 400.


| Funzionalità | Numero massimo di documenti per richiesta | 
|----------|-----------|
| Rilevamento lingua | 1000 |
| Analisi del sentiment | 10 |
| Opinion mining | 10 |
| Estrazione frasi chiave | 10 |
| Riconoscimento di entità denominate | 5 |
| Collegamento delle entità | 5 |
| Analisi del testo per la sanità  | 10 per l'API basata sul Web, 1000 per il contenitore. |
| Endpoint di analisi | 25 per tutte le operazioni. |

#### <a name="version-2"></a>[Versione 2](#tab/version-2)

| Funzionalità | Numero massimo di documenti per richiesta | 
|----------|-----------|
| Rilevamento lingua | 1000 |
| Analisi del sentiment | 1000 |
| Estrazione frasi chiave | 1000 |
| Riconoscimento di entità denominate | 1000 |
| Collegamento delle entità | 1000 |

---

## <a name="rate-limits"></a>Limiti di velocità

Il limite di velocità varia in base al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) in uso. Questi limiti sono identici per entrambe le versioni dell'API. Questi limiti di velocità non si applicano al contenitore Analisi del testo per la sanità, che non prevede un limite di velocità impostato.

| Livello          | Richieste al secondo | Richieste al minuto |
|---------------|---------------------|---------------------|
| S/multiservizio | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Le percentuali di richieste vengono misurate per ogni funzionalità di Analisi del testo separatamente. È possibile inviare il numero massimo di richieste previsto per il piano tariffario per ogni funzionalità, contemporaneamente. Ad esempio, se con il livello `S` si inviano 1000 richieste contemporaneamente, non sarà possibile inviare un'altra richiesta per 59 secondi.


## <a name="see-also"></a>Vedere anche

* [Informazioni sull'API Analisi del testo](../overview.md)
* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
