---
title: Limiti - QnA Maker
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 773c9caa2fb25b3abe4fb451bc59598c485a6110
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776256"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker

QnA Maker limiti indicati di seguito sono una combinazione dei limiti del piano [tariffario di Azure ricerca cognitiva](../../search/search-limits-quotas-capacity.md) e del piano [tariffario QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). È necessario conoscere entrambi i set di limiti per comprendere il numero di Knowledge base che è possibile creare per ogni risorsa e le dimensioni di ogni Knowledge base che possono aumentare.

## <a name="knowledge-bases"></a>Knowledge base

Il numero massimo di Knowledge base è basato sui [limiti del livello di ricerca cognitiva di Azure](../../search/search-limits-quotas-capacity.md).

|**Livello ricerca cognitiva di Azure** | **Free** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite|2|14|49|199|199|2999|

 Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice, `testkb`, viene usato per tutti gli articoli della knowledge base per la creazione e il testing.

## <a name="extraction-limits"></a>Limiti di estrazione

### <a name="file-naming-constraints"></a>Vincoli di denominazione dei file

I nomi file non possono includere i caratteri seguenti:

|Non usare il carattere|
|--|
|Virgoletta singola `'`|
|Virgolette doppie `"`|

### <a name="maximum-file-size"></a>Dimensione massima dei file

|Formato|Dimensioni massime file (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Numero massimo di file

Il numero massimo di file che è possibile estrarre e le dimensioni massime del file sono basati sui limiti del piano **[tariffario QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

### <a name="maximum-number-of-deep-links-from-url"></a>Numero massimo di collegamenti profondi dall'URL

Il numero massimo di collegamenti profondi che possono essere sottoposti a ricerca per indicizzazione per l'estrazione di QnAs da una pagina URL è **20**.

## <a name="metadata-limits"></a>Limiti di metadati

I metadati vengono presentati come coppia chiave-valore basata su testo, ad esempio `product:windows 10` . Viene archiviato e confrontato in lettere minuscole.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Da Azure ricerca cognitiva piano tariffario

Il numero massimo di campi di metadati per Knowledge base è basato sui **[limiti del livello di ricerca cognitiva di Azure](../../search/search-limits-quotas-capacity.md)**.

|**Livello ricerca cognitiva di Azure** | **Free** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

### <a name="by-name-and-value"></a>Per nome e valore

Nella tabella seguente sono elencati i caratteri di lunghezza e accettabili per il nome e il valore dei metadati.

|Item|Caratteri consentiti|Corrispondenza del criterio Regex|Caratteri max|
|--|--|--|--|
|Nome (chiave)|Consente<br>alfanumerico (lettere e cifre)<br>`_` sottolineatura<br> Non devono contenere spazi.|`^[a-zA-Z0-9_]+$`|100|
|Valore|Consente tutti gli elementi eccetto<br>`:` virgola<br>`|` (barra verticale)<br>È consentito un solo valore.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limiti di contenuto della Knowledge Base
Limiti complessivi sul contenuto nella knowledge base:
* Lunghezza del testo della risposta: 25.000
* Lunghezza del testo della domanda: 1000
* Lunghezza del testo della chiave dei metadati: 100
* Lunghezza del testo del valore dei metadati: 500
* Caratteri supportati per il nome dei metadati: alfabeti, cifre e `_`
* Caratteri supportati per il valore dei metadati: tutti tranne `:` e `|`
* Lunghezza del nome file: 200
* Formati di file supportati: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Numero massimo di domande alternative: 300
* Numero massimo di coppie domanda-risposta: dipende dal livello di **[ricerca cognitiva di Azure](../../search/search-limits-quotas-capacity.md#document-limits)** scelto. Una coppia di domande e risposte viene mappata a un documento in Azure ricerca cognitiva index.
* Pagina URL/HTML: 1 milione caratteri

## <a name="create-knowledge-base-call-limits"></a>Creare i limiti di chiamata per la Knowledge base:
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Numero massimo di domande alternative per risposta: 300
* Numero massimo di URL: 10
* Numero massimo di file: 10

## <a name="update-knowledge-base-call-limits"></a>Aggiornare i limiti di chiamata per la Knowledge base
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Lunghezza di ogni nome di origine: 300
* Numero massimo di domande alternative aggiunte o eliminate: 300
* Numero massimo di campi di metadati aggiunti o eliminati: 10
* Numero massimo di URL che è possibile aggiornare: 5

## <a name="next-steps"></a>Passaggi successivi

Informazioni su quando e come modificare i [piani tariffari dei servizi](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
