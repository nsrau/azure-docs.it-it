---
title: Modificare una knowledge base - QnA Maker
description: QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756734"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Modificare le coppie di QnA nella Knowledge base

QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.

Le coppie QnA vengono aggiunte da un'origine dati, ad esempio un file o un URL, o aggiunte come origine editoriale. Un'origine editoriale indica che la coppia QnA è stata aggiunta manualmente nel portale di QnA. Tutte le coppie di QnA sono disponibili per la modifica.

## <a name="add-an-editorial-qna-pair"></a>Aggiungere una coppia QnA editoriale
1. Accedere al portale di [QnA](https://www.qnamaker.ai/), quindi selezionare la Knowledge base a cui aggiungere la coppia di QnA.
1. Nella pagina **modifica** della Knowledge Base selezionare **Aggiungi coppia QnA** per aggiungere una nuova coppia di QnA.

1. Nella riga nuova coppia di QnA aggiungere i campi **domanda** e **risposta** richiesti. Gli altri campi sono facoltativi. Tutti i campi possono essere modificati in qualsiasi momento.

1. Facoltativamente, aggiungere formulazione **alternativa**. Il formulazione alternativa è qualsiasi forma della domanda significativamente diversa dalla domanda originale, ma deve fornire la stessa risposta.

    Quando la Knowledge base è pubblicata e l' [apprendimento attivo](use-active-learning.md) è attivato, QnA Maker raccoglie opzioni di formulazione alternative per l'accettazione. Queste opzioni sono selezionate per aumentare l'accuratezza della stima.

1. Facoltativamente, aggiungere i **metadati**. Per visualizzare i metadati, scegliere **Visualizza opzioni** dal menu di scelta rapida. I metadati forniscono filtri alle risposte fornite dall'applicazione client, ad esempio un bot di chat.

1. Facoltativamente, aggiungere **richieste di completamento**. Le richieste di completamento forniscono ulteriori percorsi di conversazione all'applicazione client da presentare all'utente.

1. Selezionare **Salva e** Esegui il training per visualizzare le stime, inclusa la nuova coppia QnA.

## <a name="edit-a-qna-pair"></a>Modificare una coppia di QnA

Qualsiasi campo in qualsiasi coppia di QnA può essere modificato, indipendentemente dall'origine dati originale. Alcuni campi potrebbero non essere visibili a causa delle impostazioni correnti delle **Opzioni di visualizzazione** , disponibili nella barra degli strumenti del contesto.

## <a name="delete-a-qna-pair"></a>Eliminare una coppia domanda/risposta

Per eliminare una coppia domanda/risposta, fare clic sull'icona **Elimina** all'estrema destra della riga. Si tratta di un'operazione definitiva. Non può essere modificata. Provare a esportare la Knowledge base dalla pagina di **pubblicazione** prima di eliminare i set.

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

Aggiungere i set di metadati selezionando prima **Opzioni di visualizzazione**, quindi selezionando Mostra **metadati**. Verrà visualizzata la colonna di metadati. Selezionare quindi il **+** segno per aggiungere un set di metadati. Questo set è costituito da una chiave e un valore.

## <a name="save-changes-to-the-qna-pairs"></a>Salva le modifiche apportate alle coppie QnA

Selezionare periodicamente **Salva e** Esegui il training dopo avere apportato modifiche per evitare di perdere le modifiche.

![Aggiungere metadati](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare a una Knowledge Base](./collaborate-knowledge-base.md)

* [Gestire le risorse di Azure usate da QnA Maker](set-up-qnamaker-service-azure.md)