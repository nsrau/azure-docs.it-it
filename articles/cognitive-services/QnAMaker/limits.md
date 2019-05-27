---
title: Limiti - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/22/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: ce6c5f3059041d8dbb097470cf4a415e73d9156b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237261"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker
Elenco completo dei limiti di QnA Maker.

## <a name="knowledge-bases"></a>Knowledge base

* Numero massimo di knowledge base in base ai [limiti di livello di ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite|2|14|49|199|199|2999|

 Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice, `testkb`, viene usato per tutti gli articoli della knowledge base per la creazione e il testing. 

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
* Numero massimo di domande alternative: 300
* Numero massimo di coppie di risposta alla domanda: Dipende i [livello ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) scelto. Una coppia di domanda e la risposta viene eseguito il mapping a un documento nell'indice di ricerca di Azure. 

## <a name="create-knowledge-base-call-limits"></a>Creare i limiti di chiamata per la Knowledge base:
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Numero massimo di domande alternative per ogni risposta: 300
* Numero massimo di URL: 10
* Numero massimo di file: 10

## <a name="update-knowledge-base-call-limits"></a>Aggiornare i limiti di chiamata per la Knowledge base
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Lunghezza di ogni nome di origine: 300
* Numero massimo di domande alternative aggiunte o eliminate: 300
* Numero massimo di campi di metadati aggiunti o eliminati: 10
* Numero massimo di URL che è possibile aggiornare: 5

## <a name="next-steps"></a>Passaggi successivi

Informazioni su quando e come modificare i livelli di servizio:

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Quando è necessario disporre di più file di origine o documenti più grandi nella tua knowledge base, oltre il livello corrente, aggiornare il servizio QnA Maker piano tariffario.
* [Servizio app](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Quando la knowledge base deve servire più richieste dall'applicazione client, aggiornare il piano tariffario del servizio app.
* [Ricerca di Azure](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Quando si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio di Ricerca di Azure.
