---
title: Limiti - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b90b4806e86ed0ba33500cf31a6ed892241ceabe
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423447"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker

QnA Maker limiti indicati di seguito sono una combinazione dei limiti del piano tariffario di [ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) e dei limiti del piano tariffario di [QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). È necessario conoscere entrambi i set di limiti per comprendere il numero di Knowledge base che è possibile creare per ogni risorsa e le dimensioni di ogni Knowledge base che possono aumentare.

## <a name="knowledge-bases"></a>Knowledge base

Il numero massimo di Knowledge base è basato sui [limiti del livello di ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite|2|14|49|199|199|2999|

 Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice, `testkb`, viene usato per tutti gli articoli della knowledge base per la creazione e il testing. 

## <a name="extraction-limits"></a>Limiti di estrazione

### <a name="maximum-number-of-files"></a>Numero massimo di file

Il numero massimo di file che è possibile estrarre e le dimensioni massime del file sono basati sui limiti del piano **[tariffario QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Numero massimo di collegamenti profondi dall'URL

Il numero massimo di collegamenti profondi che possono essere sottoposti a ricerca per indicizzazione per l'estrazione di QnAs da una pagina URL è **20**.

## <a name="metadata-limits"></a>Limiti di metadati

Il numero massimo di campi di metadati per Knowledge base è basato sui **[limiti del livello di ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Livello di ricerca di Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

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
* Numero massimo di coppie di risposta alla domanda: Dipende dal livello di **[ricerca di Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** scelto. Una coppia di domande e risposte viene mappata a un documento nell'indice di ricerca di Azure. 
* URL/pagina HTML: 1 milione caratteri

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

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Quando è necessario disporre di più file di origine o documenti più grandi nella Knowledge base, oltre al livello corrente, aggiornare il piano tariffario del servizio QnA Maker.
* [Servizio app](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Quando la knowledge base deve servire più richieste dall'applicazione client, aggiornare il piano tariffario del servizio app.
* [Ricerca di Azure](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Quando si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio di Ricerca di Azure.
