---
title: Formato Markdown - QnA Maker
description: Di seguito è riportato l'elenco dei formati markdown che è possibile utilizzare nel testo della risposta di QnA Maker.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045402"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formato Markdown supportato nel testo della risposta QnA Maker

QnA Maker memorizza il testo della risposta come markdown. Ci sono molti sapori di markdown. Per assicurarsi che il testo della risposta venga restituito e visualizzato correttamente, utilizzare questo riferimento.

Usare l'esercitazione **[CommonMark](https://commonmark.org/help/tutorial/index.html)** per convalidare il codice Markdown. L'esercitazione contiene la funzionalità **Prova** per la convalida veloce copia/incolla.

## <a name="supported-markdown-format"></a>Formato markdown supportato

Di seguito è riportato l'elenco dei formati markdown che è possibile utilizzare nel testo della risposta di QnA Maker.

|Scopo|Format|Markdown di esempio|Rendering<br>come visualizzato nel bot Chat|
|--|--|--|--|
Una nuova riga tra due frasi.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formattare una nuova riga tra due frasi](./media/qnamaker-concepts-datasources/format-newline.png)|
|Intestazioni da h1 a h6, il numero di `#` indica l'intestazione. 1 `#` è l'h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formato con intestazioni markdown](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formato con intestazioni markdown da H1 a H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|corsivo |`*text*`|`How do I create a bot with *QnA Maker*?`|![formato con corsivo](./media/qnamaker-concepts-datasources/format-italics.png)|
|Forte (grassetto)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formato con marcatura forte per grassetto](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL per il collegamento|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato URL (collegamento ipertestuale)](./media/qnamaker-concepts-datasources/format-url.png)|
|- URL per l'immagine pubblica|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato per l'URL dell'immagine pubblica ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|barrato|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato per barrato](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Grassetto e corsivo|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formato per grassetto e corsivo](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL grassetto per il collegamento|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formato per l'URL in grassetto](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL corsivo per il collegamento|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato per l'URL corsivo](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Simboli di markdown di escape|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formato per l'URL corsivo](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Elenco ordinato|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Nell'esempio precedente viene utilizzata la numerazione automatica incorporata in markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Nell'esempio precedente viene utilizzata la numerazione esplicita.|![formato per l'elenco ordinato](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Elenco non ordinato|`\n * item1 \n * item2`<br>o<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formato per l'elenco non ordinato](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Elenchi annidati|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>È possibile nidificare elenchi ordinati e non ordinati insieme. La scheda `\t`, , indica il livello di rientro dell'elemento figlio.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato per l'elenco non ordinato nidificato](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato per l'elenco ordinato nidificato](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

QnA Maker non elabora l'immagine in alcun modo. È il ruolo dell'applicazione client per il rendering dell'immagine.

Se si desidera aggiungere contenuto utilizzando le API della Knowledge Base di aggiornamento/sostituzione e il contenuto/file contiene tag html, è possibile mantenere il codice HTML nel file assicurandosi che l'apertura e la chiusura dei tag vengano convertite nel formato codificato.

| Mantieni HTML  | Rappresentazione nella richiesta API  | Rappresentazione in KB |
|-----------|---------|-------------------------|
| Sì | \&lt;br\&gt; | &lt;Br&gt; |
| Sì | \&lt;h3\&gt;intestazione\&lt;/h3\&gt; | &lt;h3&gt;&lt;intestazione /h3&gt; |

Inoltre, CR LF('r'n) vengono convertiti in n nella KB. LF('n) viene mantenuto così com'è. Se si desidera eseguire l'escape di qualsiasi sequenza di escape, ad\\\\\\\\esempio un'opzione di escape, ad esempio una barra rovesciata, ad esempio: ' r n' e '\\\\t'

## <a name="next-steps"></a>Passaggi successivi

Esaminare i formati di [file](reference-tsv-format-batch-testing.md)di test batch .
