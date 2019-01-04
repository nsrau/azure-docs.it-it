---
title: Limiti - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 772153040ac76f4b7bbee55c48527a841fc69037
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084789"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker
Elenco completo dei limiti di QnA Maker.

## <a name="knowledge-bases"></a>Knowledge base

* Numero massimo di knowledge base in base ai [limiti di livello di ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite (indici massimi -- 1 (riservato al test)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Limiti di estrazione
* Numero massimo di file che possono essere estratti e dimensione massima dei file: vedere [Prezzi di QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Numero massimo di deep link su cui può essere eseguita la ricerca per l'estrazione di QnA dalle pagine HTML delle FAQ: 20

## <a name="metadata-limits"></a>Limiti di metadati
* Numero massimo di knowledge base in base ai [limiti di livello di ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1.000|100*|1.000|1.000|1.000|1.000|

## <a name="knowledge-base-content-limits"></a>Limiti di contenuto della Knowledge Base
Limiti complessivi sul contenuto nella knowledge base:
* Lunghezza del testo della risposta: 25.000
* Lunghezza del testo della domanda: 1.000
* Lunghezza del testo di chiave/valore dei metadati: 100
* Caratteri supportati per il nome dei metadati: caratteri alfabetici, cifre e _  
* Caratteri supportati per il valore dei metadati: tutti tranne : e | 
* Lunghezza del nome file: 200
* Formati di file supportati: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Numero massimo di domande alternative: 100
* Numero massimo di coppie di risposta alla domanda: dipende dal [livello di Ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) scelto 

## <a name="create-knowledge-base-call-limits"></a>Creare i limiti di chiamata per la Knowledge base:
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Numero massimo di domande alternative per ogni risposta: 100
* Numero massimo di URL: 10
* Numero massimo di file: 10

## <a name="update-knowledge-base-call-limits"></a>Aggiornare i limiti di chiamata per la Knowledge base
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Lunghezza di ogni nome di origine: 300
* Numero massimo di domande alternative aggiunte o eliminate: 100
* Numero massimo di campi di metadati aggiunti o eliminati: 10
* Numero massimo di URL che è possibile aggiornare: 5
