---
title: Limiti - QnA Maker
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273347"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker

I limiti di QnA Maker forniti di seguito sono una combinazione dei limiti del piano tariffario di [Ricerca cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) di Azure e dei limiti del [piano tariffario QnA Maker.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) È necessario conoscere entrambi i set di limiti per comprendere il numero di knowledge base che è possibile creare per ogni risorsa e le dimensioni di ogni Knowledge Base.

## <a name="knowledge-bases"></a>Basi di conoscenza

Il numero massimo di knowledge base è basato sui [limiti del livello Ricerca cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)di Azure.

|**Livello Ricerca cognitiva di AzureAzure Cognitive Search tier** | **Gratuito** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite|2|14|49|199|199|2999|

 Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice, `testkb`, viene usato per tutti gli articoli della knowledge base per la creazione e il testing.

## <a name="extraction-limits"></a>Limiti di estrazione

### <a name="file-naming-constraints"></a>Vincoli di denominazione dei file

I nomi dei file non possono includere i seguenti caratteri:

|Non utilizzare caratteri|
|--|
|Virgoletta singola`'`|
|Virgoletta doppia`"`|

### <a name="maximum-file-size"></a>Dimensione massima dei file

|Format|Dimensioni massime dei file (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Numero massimo di file

Il numero massimo di file che è possibile estrarre e la dimensione massima del file si basa sui limiti del livello tariffario di **[QnA Maker.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**

### <a name="maximum-number-of-deep-links-from-url"></a>Numero massimo di collegamenti diretti dall'URL

Il numero massimo di collegamenti diretti che è possibile eseguire la ricerca per indicizzazione per l'estrazione di QnA da una pagina URL è **20**.

## <a name="metadata-limits"></a>Limiti di metadati

I metadati vengono presentati come una coppia `product:windows 10`chiave:valore basata su testo, ad esempio . Viene memorizzato e confrontato in minuscolo.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Per piano di Azure Cognitive Search

Il numero massimo di campi di metadati per Knowledge Base è basato sui **[limiti del livello Ricerca cognitiva di Azure.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**

|**Livello Ricerca cognitiva di AzureAzure Cognitive Search tier** | **Gratuito** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1.000|100*|1.000|1.000|1.000|1.000|

### <a name="by-name-and-value"></a>Per nome e valore

La lunghezza e i caratteri accettabili per il nome e il valore dei metadati sono elencati nella tabella seguente.

|Elemento|Caratteri consentiti|Corrispondenza del modello Regex|Numero massimo di caratteri|
|--|--|--|--|
|Nome (chiave)|Permette<br>alfanumerici (lettere e cifre)<br>`_`(sottolineatura)<br> Non deve contenere spazi.|`^[a-zA-Z0-9_]+$`|100|
|valore|Permette tutto tranne<br>`:`(due punti)<br>`|`(tubo verticale)<br>È consentito un solo valore.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limiti di contenuto della Knowledge Base
Limiti complessivi sul contenuto nella knowledge base:
* Lunghezza del testo della risposta: 25.000
* Lunghezza del testo della domanda: 1000
* Lunghezza del testo di chiave/valore: 100
* Caratteri supportati per il nome dei metadati: alfabeti, cifre e`_`
* Caratteri supportati per il `:` valore dei metadati: Tutti tranne e`|`
* Lunghezza del nome file: 200
* Formati di file supportati: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Numero massimo di domande alternative: 300
* Numero massimo di coppie domanda-risposta: dipende dal **[livello ricerca cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** di Azure scelto. Una coppia di domande e risposte esegue il mapping a un documento nell'indice di Ricerca cognitiva di Azure.A question and answer pair maps to a document on Azure Cognitive Search index.
* Pagina URL/HTML: 1 milione di caratteri

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

Informazioni su quando e come modificare [i piani tariffari](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)del servizio.
