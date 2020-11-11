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
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 905dde6932afb440c34bcccb563bfda98f23eb7c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363834"
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
| Dimensioni massime di un singolo documento | 5\.120 caratteri misurati in base a [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Si applica anche al contenitore Analisi del testo per la sanità. |
| Dimensioni massime dell'intera richiesta | 1 MB. Si applica anche al contenitore Analisi del testo per la sanità. |

Il numero massimo di documenti che è possibile inviare in una singola richiesta dipende dalla versione dell'API e dalla funzionalità in uso.

#### <a name="version-3"></a>[Versione 3](#tab/version-3)

Nell'API v3 sono stati modificati i limiti seguenti. Il superamento dei limiti indicati di seguito provocherà un codice errore HTTP 400.


| Funzionalità | Numero massimo di documenti per richiesta | 
|----------|-----------|
| Rilevamento lingua | 1000 |
| Analisi del sentiment | 10 |
| Estrazione frasi chiave | 10 |
| Riconoscimento di entità denominate | 5 |
| Collegamento delle entità | 5 |
| Contenitore Analisi del testo per la sanità | 1000 |
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

Le richieste vengono misurate separatamente per ogni funzionalità di Analisi del testo. Ad esempio, è possibile inviare il numero massimo di richieste per il piano tariffario per ogni funzionalità, nello stesso momento.  


## <a name="see-also"></a>Vedere anche

* [Informazioni sull'API Analisi del testo](../overview.md)
* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)