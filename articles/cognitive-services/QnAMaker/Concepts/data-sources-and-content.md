---
title: Origini dati e tipi di contenuto-QnA Maker
description: Informazioni su come importare coppie di domande e risposte da origini dati e tipi di contenuto supportati, che includono molti documenti strutturati standard, ad esempio PDF, DOCX e TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a6488d2c7611513528ff02d4e620124763912730
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353256"
---
# <a name="importing-from-data-sources"></a>Importazione da origini dati

Una Knowledge base è costituita da coppie di domande e risposte portate da URL e file pubblici.

## <a name="data-source-locations"></a>Percorsi di origine dati

Il contenuto viene inserito in una Knowledge base da un'origine dati. I percorsi delle origini dati sono **URL o file pubblici**, che non richiedono l'autenticazione.

I [file di SharePoint](../how-to/add-sharepoint-datasources.md), protetti con l'autenticazione, costituiscono l'eccezione. Le risorse di SharePoint devono essere file, non pagine Web. Se l'URL termina con un'estensione Web, ad esempio .ASPX, non verrà importato in QnA Maker da SharePoint.

## <a name="chit-chat-content"></a>Contenuto Chit-Chat

Il set di contenuti Chit-Chat è disponibile come origine dati di contenuto completa in diversi linguaggi e stili di conversazione. Può trattarsi di un punto di partenza per la personalità del bot e consentirà di risparmiare tempi e costi di scrittura da zero. Informazioni [su come aggiungere contenuto Chit-Chat](../how-to/chit-chat-knowledge-base.md) alla Knowledge base.

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file strutturato contenente `.tsv` domande e risposte. Queste informazioni consentono a QnA Maker di raggruppare le coppie domanda/risposta e di attribuirle a una specifica origine dati.

| Domanda  | Risposta  | Source (Sorgente)| Metadati (1 chiave: 1 valore) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato a più turni strutturato tramite l'importazione

È possibile creare conversazioni a più turni in un `.tsv` formato di file. Il formato offre la possibilità di creare le conversazioni a più turni analizzando i log delle chat precedenti (con altri processi, non usando QnA Maker), quindi creare il `.tsv` file tramite l'automazione. Importare il file per sostituire la Knowledge Base esistente.

> [!div class="mx-imgBorder"]
> ![Modello concettuale di 3 livelli di domande a più turni](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

La colonna per un multiturning `.tsv` , specifico per la multifunzione, è **richiesta**. Un esempio `.tsv` , illustrato in Excel, Mostra le informazioni da includere per definire gli elementi figlio a più turni:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**Displayorder** è numerico e il **DisplayText** è un testo che non deve includere Markdown.

> [!div class="mx-imgBorder"]
> ![Esempio di domande a più turni come illustrato in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Esporta come esempio

Se non si è certi di come rappresentare la coppia di QnA nel `.tsv` file:
* Usare questo [esempio scaricabile da GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* In alternativa, creare la coppia nel portale di QnA Maker, salvare ed esportare la Knowledge base per un esempio di come rappresentare la coppia.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipi di contenuto dei documenti che è possibile aggiungere a una Knowledge base
I tipi di contenuto includono molti documenti strutturati standard, ad esempio PDF, DOC e TXT.

### <a name="file-and-url-data-types"></a>Tipi di dati di file e URL

La tabella seguente riepiloga i tipi di contenuto e di formato di file supportati da QnA Maker.

|Tipo di origine|Tipo di contenuto| Esempi|
|--|--|--|
|URL|Domande frequenti<br> (con struttura piatta, a sezioni o con collegamenti ad altre pagine)<br>Pagine del supporto <br> (singola pagina di procedure dettagliate, articoli sulla risoluzione dei problemi e così via).|[Domande frequenti semplici](../troubleshooting.md), <br>[domande frequenti con collegamenti](https://www.microsoft.com/en-us/software-download/faq),<br> [domande frequenti con home page degli argomenti](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[articolo del supporto tecnico](./best-practices.md)|
|PDF/DOC|domande frequenti,<br> manuale del prodotto,<br> brochure,<br> documento,<br> volantino,<br> guida di supporto,<br> file domanda-risposta strutturato,<br> E così via.|**Senza multiturni**<br>[QnA.docxstrutturata ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [semi-structured.docxdi esempio ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [paper.pdfbianco di esempio ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Multiturn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Vantaggi di Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Vantaggi di Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|File domanda-risposta strutturato<br> (tra cui RTF, supporto HTML)|**Senza la funzionalità multiturne**:<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Multiturn**:<br>[Semplice FAQ.xlsstrutturata ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsdella superficie portatile ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|File domanda-risposta strutturato|[Esempio chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se è necessaria l'autenticazione per l'origine dati, considerare i metodi seguenti per ottenere tale contenuto in QnA Maker:

* Scaricare il file manualmente e importarlo in QnA Maker
* Aggiungere il file da un [percorso di SharePoint](../how-to/add-sharepoint-datasources.md) autenticato

### <a name="url-content"></a>Contenuto URL

È possibile importare due tipi di documenti tramite l' **URL** in QnA Maker:

* Indirizzo Web di domande frequenti
* URL di supporto

Ogni tipo indica un formato previsto.

### <a name="file-based-content"></a>Contenuto basato su file

È possibile aggiungere file a una Knowledge base da un'origine pubblica o dalla file system locale nel [portale di QnA Maker](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Linee guida per il formato del contenuto

Altre informazioni sulle [linee guida](../reference-document-format-guidelines.md) per il formato dei diversi file.

## <a name="next-steps"></a>Passaggi successivi

Comprendere quali informazioni vengono archiviate in una [coppia di domande e risposte (QnA)](question-answer-set.md).