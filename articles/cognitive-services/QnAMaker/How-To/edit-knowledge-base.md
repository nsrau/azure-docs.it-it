---
title: Modificare una knowledge base - QnA Maker
description: QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 3940f060209ea5ba55957a3673020656d45de9e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776545"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Modificare le coppie di QnA nella Knowledge base

QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.

Le coppie QnA vengono aggiunte da un'origine dati, ad esempio un file o un URL, o aggiunte come origine editoriale. Un'origine editoriale indica che la coppia QnA è stata aggiunta manualmente nel portale di QnA. Tutte le coppie di QnA sono disponibili per la modifica.

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>Aggiungere una coppia QnA editoriale

1. Accedere al portale di [QnA](https://www.qnamaker.ai/), quindi selezionare la Knowledge base a cui aggiungere la coppia di QnA.
1. Nella pagina **modifica** della Knowledge Base selezionare **Aggiungi coppia QnA** per aggiungere una nuova coppia di QnA.

    > [!div class="mx-imgBorder"]
    > ![Add QnA pair (Aggiungi coppia domanda/risposta)](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Nella riga nuova coppia di QnA aggiungere i campi domanda e risposta richiesti. Gli altri campi sono facoltativi. Tutti i campi possono essere modificati in qualsiasi momento.

1. Facoltativamente, aggiungere formulazione **[alternativa](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**. Il formulazione alternativa è qualsiasi forma della domanda significativamente diversa dalla domanda originale, ma deve fornire la stessa risposta.

    Quando la Knowledge base è pubblicata e l' [apprendimento attivo](use-active-learning.md) è attivato, QnA Maker raccoglie opzioni di formulazione alternative per l'accettazione. Queste opzioni sono selezionate per aumentare l'accuratezza della stima.

1. Facoltativamente, aggiungere i **[metadati](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**. Per visualizzare i metadati, scegliere **Visualizza opzioni** dal menu di scelta rapida. I metadati forniscono filtri alle risposte fornite dall'applicazione client, ad esempio un bot di chat.

1. Facoltativamente, aggiungere **[richieste di completamento](multiturn-conversation.md)**. Le richieste di completamento forniscono ulteriori percorsi di conversazione all'applicazione client da presentare all'utente.

1. Selezionare **Salva e** Esegui il training per visualizzare le stime, inclusa la nuova coppia QnA.

## <a name="rich-text-editing-for-answer"></a>Modifica di testo RTF per la risposta

La modifica di testo in formato RTF del testo di risposta offre lo stile Markdown da una semplice barra degli strumenti.

1. Selezionare l'area di testo per una risposta. la barra degli strumenti Editor di testo RTF viene visualizzata nella riga della coppia QnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'editor di testo RTF con la domanda e la risposta di una riga di coppia QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Il testo già presente nella risposta viene visualizzato correttamente, in quanto l'utente lo vedrà da un bot.

1. Modificare il testo. Selezionare formattazione funzionalità dalla barra degli strumenti per la modifica di testo RTF oppure utilizzare la funzionalità attiva/disattiva per passare alla sintassi di Markdown.

    > [!div class="mx-imgBorder"]
    > ![Utilizzare l'editor di testo RTF per scrivere e formattare il testo e salvarlo come Markdown.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Funzionalità dell'editor RTF|Tasti di scelta rapida|
    |--|--|
    |Consente di passare da un editor di testo RTF a un Markdown e viceversa. `</>`|CTRL+M|
    |Grassetto. **B**|CTR + LB|
    |Corsivo, indicato con una ** _I_**|CTRL+I|
    |Elenco non ordinato||
    |Elenco ordinato||
    |Stile paragrafo||
    |Image: aggiungere un'immagine disponibile da un URL pubblico.|CTRL+G|
    |Aggiungere il collegamento all'URL disponibile pubblicamente.|CTRL+K|
    |Emoticon: aggiungere da una selezione di emoticon.|CTRL+E|
    |Menu avanzato-Annulla|CTRL+Z|
    |Menu avanzato-Ripeti|CTRL+Y|

1. Aggiungere un'immagine alla risposta usando l'icona dell'immagine nella barra degli strumenti di testo RTF. L'editor sul posto richiede l'URL dell'immagine accessibile pubblicamente e il testo alternativo per l'immagine.


    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra l'editor sul posto con l'URL dell'immagine accessibile pubblicamente e il testo alternativo per l'immagine immessa.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Aggiungere un collegamento a un URL selezionando il testo nella risposta, quindi selezionando l'icona del collegamento sulla barra degli strumenti oppure selezionando l'icona del collegamento sulla barra degli strumenti e quindi immettendo il nuovo testo e l'URL.

    > [!div class="mx-imgBorder"]
    > ![Utilizzare l'editor di testo RTF per aggiungere un'immagine accessibile pubblicamente e il testo alternativo.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Modificare una coppia di QnA

Qualsiasi campo in qualsiasi coppia di QnA può essere modificato, indipendentemente dall'origine dati originale. Alcuni campi potrebbero non essere visibili a causa delle impostazioni correnti delle **Opzioni di visualizzazione** , disponibili nella barra degli strumenti del contesto.

## <a name="delete-a-qna-pair"></a>Eliminare una coppia domanda/risposta

Per eliminare una coppia domanda/risposta, fare clic sull'icona **Elimina** all'estrema destra della riga. Si tratta di un'operazione definitiva. Non può essere modificata. Prendere in considerazione la possibilità di esportare la knowledge base dalla pagina **Pubblica** prima di eliminare le coppie.

![Eliminare una coppia domanda/risposta](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Trovare l'ID della coppia di QnA

Se è necessario trovare l'ID della coppia QnA, è possibile trovarlo in due posizioni:

* Passare il puntatore del mouse sull'icona Elimina nella riga della coppia QnA a cui si è interessati. Il testo del passaggio del mouse include l'ID della coppia QnA.
* Esportare la Knowledge base. Ogni coppia di QnA nella Knowledge base include l'ID della coppia di QnA.

## <a name="add-alternate-questions"></a>Aggiungere domande alternative

Aggiungere domande alternative a una coppia domanda/risposta esistente per aumentare la probabilità di una corrispondenza con le query degli utenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Collegamento di coppie QnA

Le coppie di QnA di collegamento sono fornite con [richieste di completamento](multiturn-conversation.md). Si tratta di una connessione logica tra coppie QnA, gestita a livello di Knowledge base. È possibile modificare le richieste di completamento nel portale di QnA Maker.

Non è possibile collegare coppie QnA nei metadati della risposta.

## <a name="add-metadata"></a>Aggiungere metadati

Aggiungere le coppie di metadati selezionando prima **Opzioni di visualizzazione**, quindi selezionando **Mostra metadati**. Verrà visualizzata la colonna di metadati. Selezionare quindi il **+** segno per aggiungere una coppia di metadati. Questa coppia è costituita da una chiave e un valore.

Altre informazioni sui metadati sono disponibili nella Guida introduttiva del portale QnA Maker per i metadati:
* [Creazione e modifica - aggiungere metadati a una coppia di domanda e risposta](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Eseguire query sulle previsioni - filtrare le risposte in base ai metadati](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Salva le modifiche apportate alle coppie QnA

Selezionare periodicamente **Salva e** Esegui il training dopo avere apportato modifiche per evitare di perdere le modifiche.

![Aggiungere metadati](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando utilizzare la modifica di testo RTF rispetto a Markdown

La modifica delle risposte con [testo RTF](#add-an-editorial-qna-set) consente, come autore, di utilizzare una barra degli strumenti di formattazione per selezionare e formattare rapidamente il testo.

[Markdown](../reference-markdown-format.md) è uno strumento migliore quando è necessario generare automaticamente contenuto per creare Knowledge base da importare come parte di una pipeline di integrazione continua/recapito continuo o per i [test batch](../Quickstarts/batch-testing.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare a una Knowledge Base](./collaborate-knowledge-base.md)

* [Gestire le risorse di Azure usate da QnA Maker](set-up-qnamaker-service-azure.md)
