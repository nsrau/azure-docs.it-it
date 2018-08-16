---
title: Limiti di QnA Maker - Servizi cognitivi di Azure | Microsoft Docs
description: Limiti di QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93471faab9aac94616c770cbee21fb0364f73639
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501888"
---
# <a name="qna-maker-limits"></a>Limiti di QnA Maker
Elenco completo dei limiti di QnA Maker.

## <a name="knowledge-bases"></a>Knowledge base

* Numero massimo di knowledge base in base ai [limiti di livello di ricerca di Azure](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite (indici massimi -- 1 (riservato al test)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Limiti di estrazione
* Numero massimo di file che possono essere estratti e dimensione massima dei file: vedere [Prezzi di QnAMaker](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Numero massimo di deep link su cui può essere eseguita la ricerca per l'estrazione di QnA dalle pagine HTML delle FAQ: 20

## <a name="metadata-limits"></a>Limiti di metadati
* Numero massimo di knowledge base in base ai [limiti di livello di ricerca di Azure](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1000|100*|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Limiti di contenuto della Knowledge Base
Limiti complessivi sul contenuto nella knowledge base:
* Lunghezza del testo di risposta: 250000
* Lunghezza del testo della domanda: 1000
* Lunghezza del testo di chiave/valore: 100
* Caratteri supportati per il nome dei metadati: caratteri alfabetici, cifre e _  
* Caratteri supportati per il nome dei metadati: caratteri alfabetici, cifre e | 
* Lunghezza del nome file: 200
* Formati di file supportati: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Numero massimo di domande alternative: 100
* Numero massimo di coppie di risposta alla domanda: dipende dal [livello di ricerca di Azure](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) scelto 

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
