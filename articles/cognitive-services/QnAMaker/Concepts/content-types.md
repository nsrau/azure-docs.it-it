---
title: Tipi di contenuto-QnA Maker
description: I tipi di contenuto includono molti documenti strutturati standard, ad esempio PDF, DOCX e TXT.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 50924ce035d9a7dc7778cf45668dc993ee5486e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776987"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipi di contenuto dei documenti che è possibile aggiungere a una Knowledge base
I tipi di contenuto includono molti documenti strutturati standard, ad esempio PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipi di dati di file e URL

La tabella seguente riepiloga i tipi di contenuto e di formato di file supportati da QnA Maker.

|Tipo di origine|Tipo di contenuto| Esempi|
|--|--|--|
|URL|Domande frequenti<br> (con struttura piatta, a sezioni o con collegamenti ad altre pagine)<br>Pagine del supporto <br> (singola pagina di procedure dettagliate, articoli sulla risoluzione dei problemi e così via).|[Domande frequenti semplici](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[domande frequenti con collegamenti](https://www.microsoft.com/en-us/software-download/faq),<br> [domande frequenti con home page degli argomenti](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[articolo del supporto tecnico](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|domande frequenti,<br> manuale del prodotto,<br> brochure,<br> documento,<br> volantino,<br> guida di supporto,<br> file domanda-risposta strutturato,<br> E così via.|**Senza multiturni**<br>[QnA.docxstrutturata ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [semi-structured.docxdi esempio ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [paper.pdfbianco di esempio ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Multiturn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Vantaggi di Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Vantaggi di Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|File domanda-risposta strutturato<br> (tra cui RTF, supporto HTML)|**Senza la funzionalità multiturne**:<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Multiturn**:<br>[Semplice FAQ.xlsstrutturata ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsdella superficie portatile ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|File domanda-risposta strutturato|[Esempio chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se è necessaria l'autenticazione per l'origine dati, considerare i metodi seguenti per ottenere tale contenuto in QnA Maker:

* Scaricare manualmente il file e importarlo in QnA Maker
* Aggiungere un file da un [percorso di SharePoint](../how-to/add-sharepoint-datasources.md) autenticato

## <a name="url-content"></a>Contenuto URL

È possibile importare due tipi di documenti tramite l' **URL** in QnA Maker:

* Indirizzo Web di domande frequenti
* URL di supporto

Ogni tipo indica un formato previsto.

## <a name="file-based-content"></a>Contenuto basato su file

È possibile aggiungere file a una Knowledge base da un'origine pubblica o dalla file system locale nel [portale di QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Linee guida per il formato del contenuto

Altre informazioni sulle [linee guida](../reference-document-format-guidelines.md) per il formato dei diversi file.

## <a name="next-steps"></a>Passaggi successivi

Comprendere quali informazioni vengono archiviate in una [coppia di domande e risposte (QnA)](question-answer-set.md).
