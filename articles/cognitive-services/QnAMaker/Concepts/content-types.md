---
title: Tipi di contenuto-QnA Maker
description: I tipi di contenuto includono molti documenti strutturati standard, ad esempio PDF, DOC e TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 3020c0623cff73e5a907f4d63f08f5866afd52dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902082"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipi di contenuto dei documenti che è possibile aggiungere a una Knowledge base
I tipi di contenuto includono molti documenti strutturati standard, ad esempio PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipi di dati di file e URL

La tabella seguente riepiloga i tipi di contenuto e di formato di file supportati da QnA Maker.

|Tipo di origine|Content Type| Esempi|
|--|--|--|
|URL|Domande frequenti<br> (con struttura piatta, a sezioni o con collegamenti ad altre pagine)<br>Pagine del supporto <br> (singola pagina di procedure dettagliate, articoli sulla risoluzione dei problemi e così via).|[Domande frequenti semplici](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[domande frequenti con collegamenti](https://www.microsoft.com/en-us/software-download/faq),<br> [domande frequenti con home page degli argomenti](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[articolo del supporto tecnico](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|domande frequenti,<br> manuale del prodotto,<br> brochure,<br> documento,<br> volantino,<br> guida di supporto,<br> file domanda-risposta strutturato,<br> e così via.|**Senza multiturni**<br>[File domanda-risposta strutturato](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Esempio white paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Multiturn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Vantaggi di Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Vantaggi di Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|File domanda-risposta strutturato<br> (tra cui RTF, supporto HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|File domanda-risposta strutturato|[Esempio chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

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

QnA Maker aggiunge contenuto di file e URL, convertendo il contenuto in set QnA, archiviato come Markdown (`.md`). Il contenuto è adatto a una Knowledge base in cui il contenuto è organizzato in un formato strutturato e viene rappresentato in sezioni ben definite. Le sezioni possono essere ulteriormente suddivise in sottosezioni o argomenti secondari. Il procedimento di estrazione funziona meglio su contenuti che presentano una struttura chiara con intestazioni gerarchiche.

QnA Maker identifica le sezioni e le sottosezioni e le relazioni nel contenuto in base a indizi visivi come dimensioni del carattere, stile del carattere, numerazione, colori e così via. Il contenuto semi-strutturato include manuali, domande frequenti, linee guida, criteri, brochure, volantini e molti altri tipi di file.

## <a name="faq-urls"></a>Indirizzo Web di domande frequenti

QnA Maker supporta 3 diversi formati di pagine Web di domande frequenti: pagine semplici di domande frequenti, pagine di domande frequenti con collegamenti, pagine di domande frequenti con collegamenti ad altre pagine.


### <a name="plain-faq-pages"></a>Pagine semplici di domande frequenti

Si tratta del tipo più comune di pagina di domande frequenti, in cui le risposte seguono immediatamente le domande nella stessa pagina.

Di seguito è riportato un esempio di pagina semplice di domande frequenti:

![Esempio di pagina semplice di domande frequenti per una knowledge base](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Pagina di domande frequenti con collegamenti

In questo tipo di pagina di domande frequenti, le domande sono aggregate e collegate a risposte che si trovano in diverse sezioni della stessa pagina o in pagine differenti.

Di seguito è riportato un esempio di pagina di domande frequenti con collegamenti a sezioni che si trovano nella stessa pagina:

 ![Esempio di pagina di domande frequenti con collegamenti a sezioni per una knowledge base](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>Pagina di domande frequenti con collegamenti ad altre pagine

Questo tipo di pagina di domande frequenti include una home page con argomenti in cui ogni argomento è un collegamento alle relative pagine QnA in una pagina diversa. Qui, QnA Maker esegue una ricerca per indicizzazione di tutte le pagine collegate per estrarre le domande e le risposte corrispondenti.

Di seguito è riportato un esempio di pagina delle domande frequenti in cui la home page di argomenti include collegamenti a sezioni di domande frequenti in pagine diverse.

 ![Esempio di pagina di domande frequenti con collegamenti diretti per una knowledge base](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>URL del supporto

QnA Maker è in grado di elaborare pagine web di supporto semi-strutturatee, ad esempio articoli web che descrivono come eseguire una determinata attività, come diagnosticare e risolvere un problema specifico e quali sono le procedure consigliate per un determinato processo. Il procedimento di estrazione funziona meglio su contenuti che presentano una struttura chiara con intestazioni gerarchiche.

> [!NOTE]
> L'estrazione per gli articoli di supporto è una nuova funzionalità, nelle sue prime fasi. Funziona al meglio con pagine semplici, ben strutturate e che non contengono intestazioni o piè di pagina complessi.

![QnA Maker supporta l'estrazione da pagine web semi-strutturate che presentano una struttura chiara con intestazioni gerarchiche.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>File PDF/DOC

QnA Maker può elaborare il contenuto semistrutturato di un file PDF o DOC e convertirlo in file domanda-risposta. Un buon file che può inoltre essere estratto è uno in cui il contenuto è organizzato in un formato strutturato e viene rappresentato da sezioni ben definite. Le sezioni possono essere suddivise ulteriormente in sottosezioni o argomenti correlati. Il procedimento di estrazione funziona meglio su documenti che presentano una struttura chiara con intestazioni gerarchiche.

QnA Maker identifica le sezioni e le sottosezioni e le relazioni nel file in base a indizi visivi come dimensioni del carattere, stile del carattere, numerazione, colori e così via. I file PDF o DOC semi-strutturati possono essere manuali, domande frequenti, linee guida, criteri, brochure, volantini e molti altri tipi di file. Di seguito alcuni esempi di questi tipi di file.

### <a name="product-manuals"></a>Manuali di prodotti

Un manuale è in genere materiale di riferimento fornito insieme a un prodotto. Consente all'utente di configurare, usare e gestire un prodotto e di risolverne i problemi. Quando QnA Maker elabora un manuale, estrae i titoli e sottotitoli come domande e il contenuto successivo come risposte. Vedere un esempio [qui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Di seguito è riportato un esempio di un manuale con una pagina di indice e con contenuto gerarchico

 ![Esempio di manuale del prodotto per una knowledge base](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> L'estrazione funziona al meglio con i manuali che hanno un sommario e/o una pagina di indice e una struttura chiara con titoli gerarchici.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochure, linee guida, documenti e altri file

Possono essere elaborati anche molti altri tipi di documento in modo che possano generare coppie di controllo qualità, purché essi dispongano di una struttura e di un layout chiari. Sono inclusi: brochure, linee guida, report, white paper, articoli scientifici, criteri, libri e così via. Vedere un esempio [qui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Di seguito è riportato un esempio di documento semistrutturato, senza un indice:

 ![Documento semistrutturato di Archiviazione Blob di Azure](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento domanda-risposta strutturato

Il formato per documenti domanda-risposta strutturati in file con estensione DOC consiste nell'alternanza di domande e risposte per ogni linea, una domanda per ogni linea seguita dalla relativa risposta nella linea seguente, come illustrato di seguito:

```text
Question1

Answer1

Question2

Answer2
```

Di seguito è riportato un esempio di un documento di Word di domanda-risposta strutturato:

 ![Esempio di documento domanda-risposta strutturato per una knowledge base](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>File *TXT*, *TSV* e *XLS* strutturati

Anche i documenti domanda-risposta sotto forma di file *.txt*, *.tsv* oppure *.xls* strutturati possono essere caricati da QnA Maker per creare o estendere una knowledge base.  Questi file possono essere testo normale, o possono disporre di contenuto in formato RTF o HTML.

| Domanda  | Risposta  | Metadati (1 chiave: 1 valore) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Qualsiasi colonna aggiuntiva nel file di origine viene ignorata.

### <a name="example-of-structured-excel-file"></a>Esempio di file di Excel strutturato

Di seguito è riportato un esempio di file *.xls* domanda-risposta strutturato, con contenuto HTML:

 ![Esempio di file Excel domanda-risposta strutturato per una knowledge base](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Esempio di domande alternative per una singola risposta in un file di Excel

Di seguito è riportato un esempio di un file QnA *. xls* strutturato, con diverse domande alternative per una singola risposta:

 ![Esempio di domande alternative per una singola risposta in un file di Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Una volta importato il file, la coppia di domande e risposte si trova nella Knowledge base, come illustrato di seguito:

 ![Screenshot delle domande alternative per la singola risposta importata nella Knowledge base](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Considerazioni di formattazione

Dopo aver importato un file o un URL, QnA Maker converte e archivia il contenuto nel [formato Markdown](https://en.wikipedia.org/wiki/Markdown). Il processo di conversione aggiunge nuove righe nel testo, ad esempio `\n\n`. Una conoscenza del formato Markdown consente di comprendere il contenuto convertito e gestire il contenuto della Knowledge base.

Se si aggiunge o modifica il contenuto direttamente nella Knowledge base, utilizzare la **formattazione Markdown** per creare contenuto RTF o modificare il contenuto del formato Markdown già presente nella risposta. QnA Maker supporta gran parte del formato Markdown per fornire funzionalità di testo avanzate ai contenuti. Tuttavia, l'applicazione client, ad esempio un bot di chat, potrebbe non supportare lo stesso set di formati Markdown. È importante testare la visualizzazione delle risposte dell'applicazione client.

Per altre informazioni, vedere la [documentazione di riferimento di QnA Maker Markdown](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Test di Markdown

Usare l'esercitazione **[CommonMark](https://commonmark.org/help/tutorial/index.html)** per convalidare il codice Markdown. L'esercitazione contiene la funzionalità **Prova** per la convalida veloce copia/incolla.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come progettare e gestire [set di domande e risposte (QnA)](question-answer-set.md)