---
title: Formato Markdown-QnA Maker
description: Di seguito è riportato l'elenco dei formati Markdown che è possibile usare nel testo di risposta QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352270"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formato Markdown supportato nel testo della risposta QnA Maker

QnA Maker archivia il testo di risposta come Markdown. Sono disponibili molti tipi di Markdown. Per assicurarsi che il testo della risposta venga restituito e visualizzato correttamente, usare questo riferimento.

Usare l'esercitazione **[CommonMark](https://commonmark.org/help/tutorial/index.html)** per convalidare il codice Markdown. L'esercitazione contiene la funzionalità **Prova** per la convalida veloce copia/incolla.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando utilizzare la modifica di testo RTF rispetto a Markdown

La modifica delle risposte con [testo RTF](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) consente, come autore, di utilizzare una barra degli strumenti di formattazione per selezionare e formattare rapidamente il testo.

Markdown è uno strumento migliore quando è necessario generare automaticamente contenuto per creare Knowledge base da importare come parte di una pipeline di integrazione continua/recapito continuo o per i [test batch](./index.yml).

## <a name="supported-markdown-format"></a>Formato Markdown supportato

Di seguito è riportato l'elenco dei formati Markdown che è possibile usare nel testo di risposta QnA Maker.

|Scopo|Formato|Markdown di esempio|Rendering<br>come visualizzato in chat bot|
|--|--|--|--|
Nuova riga tra due frasi.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formattare la nuova riga tra due frasi](./media/qnamaker-concepts-datasources/format-newline.png)|
|Intestazioni da H1 a H6, il numero di `#` indica l'intestazione. 1 `#` è il H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formattare con le intestazioni Markdown](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formattare con le intestazioni Markdown da H1 a H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Corsivo |`*text*`|`How do I create a bot with *QnA Maker*?`|![formattare con corsivo](./media/qnamaker-concepts-datasources/format-italics.png)|
|Forte (grassetto)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formato con contrassegno forte per grassetto](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL per il collegamento|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato per URL (collegamento ipertestuale)](./media/qnamaker-concepts-datasources/format-url.png)|
|* URL per l'immagine pubblica|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato dell'URL dell'immagine pubblica ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|barrato|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato per barrato](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Grassetto e corsivo|`***text**_`|`How can I create a _*_QnA Maker_*_ bot?`|![formato per grassetto e corsivo](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL grassetto per il collegamento|`[_*text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formattare l'URL in grassetto](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL corsivo per il collegamento|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato per l'URL corsivo](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Caratteri di escape Markdown|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Formato per i simboli Markdown di escape.](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Elenco ordinato|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Nell'esempio precedente viene usata la numerazione automatica incorporata in Markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Nell'esempio precedente viene usata la numerazione esplicita.|![formato dell'elenco ordinato](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Elenco non ordinato|`\n * item1 \n * item2`<br>oppure<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formato dell'elenco non ordinato](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Elenchi annidati|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>È possibile annidare insieme elenchi ordinati e non ordinati. La scheda, `\t` , indica il livello di rientro dell'elemento figlio.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato per un elenco non ordinato annidato](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato dell'elenco ordinato annidato](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker non elabora l'immagine in alcun modo. Si tratta del ruolo dell'applicazione client per il rendering dell'immagine.

Se si desidera aggiungere contenuto utilizzando le API Update/Replace Knowledge base e il contenuto o il file contiene tag HTML, è possibile mantenere il codice HTML nel file assicurando che l'apertura e la chiusura dei tag vengano convertite nel formato codificato.

| Mantieni HTML  | Rappresentazione nella richiesta API  | Rappresentazione in KB |
|-----------|---------|-------------------------|
| Sì | \&lt; br \& gt; | &lt;br&gt; |
| Sì | \&lt; H3 \& gt; intestazione \& lt;/H3 \& gt; | &lt;&gt;/H3 intestazione &lt; H3&gt; |

Inoltre, CR LF (\r\n) viene convertito in \n nella KB. LF (\n) viene mantenuto così com'è. Se si vuole eseguire l'escape di qualsiasi sequenza di escape come \t o \n, è possibile usare la barra rovesciata, ad esempio:' \\ \\ r \\ \\ n'è \\ \\ t'

## <a name="next-steps"></a>Passaggi successivi

Esaminare i [formati di file](reference-tsv-format-batch-testing.md)di test batch.