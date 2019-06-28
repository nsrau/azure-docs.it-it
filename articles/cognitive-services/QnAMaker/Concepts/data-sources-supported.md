---
title: Origini dati supportate - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker estrae automaticamente coppie di domanda-risposta dal contenuto semistrutturato, ad esempio le domande frequenti, i manuali di prodotti, le linee guida, i documenti di supporto e i criteri archiviati come pagine Web, file PDF o file Microsoft Word. I contenuti possono anche essere aggiunti alla Knowledge Base da file di contenuto QnA strutturati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: diberry
ms.openlocfilehash: efb2524b430935e6c74415efe850b69835825bc7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447681"
---
# <a name="data-sources-for-qna-maker-content"></a>Origini dati per i contenuti QnA Maker

QnA Maker estrae automaticamente coppie di domande e risposte da contenuto semistrutturato come domande frequenti, manuali di prodotto, linee guida, documenti di supporto e criteri archiviati come pagine Web, file PDF o file di documento di Microsoft Word. I contenuti possono anche essere aggiunti alla Knowledge Base da file di contenuto QnA strutturati. 

La tabella seguente riepiloga i tipi di contenuto e di formato di file supportati da QnA Maker.

|Tipo di origine|Content Type| Esempi|
|--|--|--|
|URL|Domande frequenti<br> (con struttura piatta, a sezioni o con collegamenti ad altre pagine)<br>Pagine del supporto <br> (singola pagina di procedure dettagliate, articoli sulla risoluzione dei problemi e così via).|[Domande frequenti semplici](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[domande frequenti con collegamenti](https://www.microsoft.com/software-download/faq),<br> [domande frequenti con home page degli argomenti](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[articolo del supporto tecnico](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|domande frequenti,<br> manuale del prodotto,<br> brochure,<br> documento,<br> volantino,<br> guida di supporto,<br> file domanda-risposta strutturato,<br> e così via.|[File domanda-risposta strutturato](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|File domanda-risposta strutturato<br> (tra cui RTF, supporto HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|File domanda-risposta strutturato|[Esempio chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Percorsi di origine dati

La maggior parte dei percorsi di origine di dati necessario fornire l'URL pubblico o file, che non richiedono l'autenticazione. 

[Località dell'origine dati SharePoint](../How-to/add-sharepoint-datasources.md) possono fornire file autenticati. Risorse di SharePoint devono essere file, non le pagine web. 

Se si dispone di un file autenticato o un URL, un'opzione alternativa consiste nello scaricare il file dal sito di autenticazione nel computer locale, quindi aggiungere il file dal computer locale i computer alla knowledge base. 

## <a name="faq-urls"></a>Indirizzo Web di domande frequenti

QnA Maker può supportare tre diversi formati di pagine Web di domande frequenti: pagine semplici di domande frequenti, pagine di domande frequenti con collegamenti, pagine di domande frequenti con collegamenti ad altre pagine.

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

QnA Maker identifica sezioni, sottosezioni e relazioni nel file basandosi su indizi visivi, ad esempio le dimensione del carattere,lo stile del carattere, i numeri, i colori e così via. I file PDF o DOC semistrutturati potrebbero essere manuali, domande frequenti, linee guida, criteri, brochure, volantini e molti altri tipi. Di seguito alcuni esempi di questi tipi di file.

### <a name="product-manuals"></a>Manuali di prodotti

Un manuale è in genere materiale di riferimento fornito insieme a un prodotto. Consente all'utente di configurare, usare e gestire un prodotto e di risolverne i problemi. Quando QnA Maker elabora un manuale, estrae i titoli e sottotitoli come domande e il contenuto successivo come risposte. Vedere un esempio [qui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Di seguito è riportato un esempio di un manuale con una pagina di indice e con contenuto gerarchico

 ![Esempio di manuale del prodotto per una knowledge base](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> L'estrazione funziona al meglio con i manuali che hanno un sommario e/o una pagina di indice e una struttura chiara con titoli gerarchici.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochure, linee guida, documenti e altri file

Possono essere elaborati anche molti altri tipi di documento in modo che possano generare coppie di controllo qualità, purché essi dispongano di una struttura e di un layout chiari. incluse le seguenti: brochure, linee guida, report, white paper, documenti scientifici, criteri, documentazioni e così via. Vedere un esempio [qui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

| Domanda  | Risposta  | I metadati (1 tasto: 1 valore) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Qualsiasi colonna aggiuntiva nel file di origine viene ignorata.

Di seguito è riportato un esempio di file *.xls* domanda-risposta strutturato, con contenuto HTML:

 ![Esempio di file Excel domanda-risposta strutturato per una knowledge base](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file TSV strutturato che contiene informazioni sull'origine dei dati. Queste informazioni consentono a QnA Maker di raggruppare le coppie domanda/risposta e di attribuirle a una specifica origine dati.

| Domanda  | Risposta  | `Source`| I metadati (1 tasto: 1 valore) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Aggiunta di contenuti a livello editoriale alla knowledge base

Se non è presente contenuto pre-esistente per popolare la Knowledge Base, è possibile aggiungere contenuti domanda-risposta a livello editoriale nella Knowledge Base di QnA Maker. Informazioni su come aggiornare la Knowledge Base sono disponibili [qui](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Considerazioni di formattazione

Dopo aver importato un file o un URL, questo viene convertito in Markdown e archiviato in questo formato. Se il processo di conversione non converte correttamente i collegamenti nei file e gli URL, è consigliabile modificare le domande e risposte nella pagina **Modifica**. 

|Format|Scopo|
|--|--|
|`\n\n`| Nuova riga|
|`\n*`|Punto elenco per elenco ordinato|

## <a name="editing-your-knowledge-base-locally"></a>Modificare la knowledge base in locale

Dopo aver creato una knowledge base, si consiglia di apportare modifiche al testo della knowledge base nel [portale di QnA Maker](https://qnamaker.ai), invece di esportare e reimportare tramite file locali. Tuttavia, potrebbe essere talvolta necessario modificare una knowledge base in locale. 

Esportare la knowledge base dalla pagina **Impostazioni** e modificarla con Microsoft Excel. Se si sceglie di usare un'altra applicazione per modificare il file TSV esportato, l'applicazione può comportare errori di sintassi perché non è completamente conforme al formato TSV. File TSV Microsoft Excel, in genere non introducono eventuali errori di formattazione. 

Dopo aver completato le modifiche, reimportare il file TSV dalla pagina **Impostazioni**. In questo modo si sostituirà completamente la knowledge base corrente con la knowledge base importata. 

## <a name="testing-your-markdown"></a>Test di Markdown

Usare l'esercitazione **[CommonMark](https://commonmark.org/help/tutorial/index.html)** per convalidare il codice Markdown. L'esercitazione contiene la funzionalità **Prova** per la convalida veloce copia/incolla. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Vedere anche 

[Panoramica di QnA Maker](../Overview/overview.md)
