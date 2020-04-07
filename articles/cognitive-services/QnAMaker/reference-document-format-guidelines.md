---
title: Importare le linee guida per il formato del documento - QnA Maker
description: Comprendere in che modo i tipi di URL vengono utilizzati per importare e creare set QnA.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1e006520ce5c61f2da3f425af2332b0d41cc3881
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754899"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Linee guida per il formato di documenti e URL importati

Consulta queste linee guida per la formattazione per ottenere i migliori risultati per i tuoi contenuti.

## <a name="formatting-considerations"></a>Considerazioni di formattazione

Dopo aver importato un file o un URL, QnA Maker converte e memorizza i contenuti nel [formato markdown.](https://en.wikipedia.org/wiki/Markdown) Il processo di conversione aggiunge nuove `\n\n`righe nel testo, ad esempio . Una conoscenza del formato markdown consente di comprendere il contenuto convertito e gestire il contenuto della Knowledge Base.

Se si aggiunge o si modifica il contenuto direttamente nella Knowledge Base, utilizzare la **formattazione markdown** per creare contenuto RTF o modificare il contenuto in formato markdown già presente nella risposta. QnA Maker supporta gran parte del formato markdown per portare funzionalità RTF ai tuoi contenuti. Tuttavia, l'applicazione client, ad esempio un bot di chat, potrebbe non supportare lo stesso set di formati markdown. È importante testare la visualizzazione delle risposte dell'applicazione client.

Vedere un elenco completo dei [tipi di contenuto e degli esempi](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Formattazione di base dei documenti

QnA Maker identifica sezioni e sottosezioni e relazioni nel file in base a indizi visivi come:

* dimensione del carattere
* stile del carattere
* Numerazione
* colori

## <a name="product-manuals"></a>Manuali di prodotti

Un manuale è in genere materiale di riferimento fornito insieme a un prodotto. Consente all'utente di configurare, usare e gestire un prodotto e di risolverne i problemi. Quando QnA Maker elabora un manuale, estrae i titoli e sottotitoli come domande e il contenuto successivo come risposte. Vedere un esempio [qui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Di seguito è riportato un esempio di un manuale con una pagina di indice e con contenuto gerarchico

 ![Esempio di manuale del prodotto per una knowledge base](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> L'estrazione funziona al meglio con i manuali che hanno un sommario e/o una pagina di indice e una struttura chiara con titoli gerarchici.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochure, linee guida, documenti e altri file

Possono essere elaborati anche molti altri tipi di documento in modo che possano generare coppie di controllo qualità, purché essi dispongano di una struttura e di un layout chiari. Questi includono: brochure, linee guida, relazioni, white paper, documenti scientifici, politiche, libri, ecc. Vedere un esempio [qui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Di seguito è riportato un esempio di documento semistrutturato, senza un indice:

 ![Documento semistrutturato di Archiviazione Blob di Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Documento domanda-risposta strutturato

Il formato per documenti domanda-risposta strutturati in file con estensione DOC consiste nell'alternanza di domande e risposte per ogni linea, una domanda per ogni linea seguita dalla relativa risposta nella linea seguente, come illustrato di seguito:

```text
Question1

Answer1

Question2

Answer2
```

Di seguito è riportato un esempio di un documento di Word di domanda-risposta strutturato:

 ![Esempio di documento domanda-risposta strutturato per una knowledge base](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>File *TXT*, *TSV* e *XLS* strutturati

Anche i documenti domanda-risposta sotto forma di file *.txt*, *.tsv* oppure *.xls* strutturati possono essere caricati da QnA Maker per creare o estendere una knowledge base.  Questi file possono essere testo normale, o possono disporre di contenuto in formato RTF o HTML.

| Domanda  | Risposta  | Metadati (1 chiave: 1 valore) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Qualsiasi colonna aggiuntiva nel file di origine viene ignorata.

### <a name="example-of-structured-excel-file"></a>Esempio di file Excel strutturato

Di seguito è riportato un esempio di file *.xls* domanda-risposta strutturato, con contenuto HTML:

 ![Esempio di file Excel domanda-risposta strutturato per una knowledge base](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Esempio di domande alternative per una singola risposta nel file Excel

Di seguito è riportato un esempio di file QnA *.xls* strutturato, con diverse domande alternative per una singola risposta:

 ![Esempio di domande alternative per una singola risposta nel file Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Dopo l'importazione del file, la coppia domanda-risposta si trova nella Knowledge Base come illustrato di seguito:

 ![Screenshot di domande alternative per una singola risposta importata nella Knowledge Base](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file TSV strutturato che contiene informazioni sull'origine dei dati. Queste informazioni consentono a QnA Maker di raggruppare le coppie domanda/risposta e di attribuirle a una specifica origine dati.

| Domanda  | Risposta  | Source (Sorgente)| Metadati (1 chiave: 1 valore) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formattazione di più documenti

* Utilizzare intestazioni e sottotitoli per indicare la gerarchia. Ad esempio, è possibile indicare la QnA padre e h2 per indicare la QnA che deve essere considerata come prompt. Utilizzare dimensioni ridotte per indicare la gerarchia successiva. Non usare lo stile, il colore o un altro meccanismo per implicare la struttura nel documento, QnA Maker non estrarrà i prompt a più turni.
* Il primo carattere dell'intestazione deve essere maiuscolo.
* Non terminare un titolo con `?`un punto interrogativo, .

**Documenti di esempio**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Vantaggi di Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Vantaggi di Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Passaggi successivi

Visualizzare un elenco completo dei [tipi di contenuto e degli esempi](./Concepts/content-types.md#file-and-url-data-types)