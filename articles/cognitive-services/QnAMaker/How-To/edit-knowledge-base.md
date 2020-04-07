---
title: Modificare una knowledge base - QnA Maker
description: QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756734"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Modificare le coppie QnA nella Knowledge Base

QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.

Le coppie QnA vengono aggiunte da un'origine dati, ad esempio un file o un URL, oppure aggiunte come origine editoriale. Una fonte editoriale indica che la coppia QnA è stata aggiunta manualmente nel portale QnA. Tutte le coppie QnA sono disponibili per la modifica.

## <a name="add-an-editorial-qna-pair"></a>Aggiungere una coppia QnA editoriale
1. Accedere al [portale QnA](https://www.qnamaker.ai/), quindi selezionare la Knowledge Base a cui aggiungere la coppia QnA.
1. Nella pagina **MODIFICA** della Knowledge Base selezionare **Aggiungi coppia QnA** per aggiungere una nuova coppia QnA.

1. Nella nuova riga della coppia QnA aggiungere i campi **Domanda** e **Risposta** necessari. Gli altri campi sono facoltativi. Tutti i campi possono essere modificati in qualsiasi momento.

1. Facoltativamente, aggiungere **formulazioni alternative.** La formulazione alternativa è qualsiasi forma della domanda che è significativamente diversa dalla domanda originale, ma dovrebbe fornire la stessa risposta.

    Quando la Knowledge Base viene pubblicata e [l'apprendimento attivo](use-active-learning.md) è attivato, QnA Maker raccoglie scelte di formulazione alternative da accettare. Queste scelte vengono selezionate per aumentare l'accuratezza della stima.

1. Facoltativamente, aggiungere **metadati**. Per visualizzare i metadati, selezionate **Opzioni di visualizzazione** nel menu di scelta rapida. I metadati forniscono filtri alle risposte fornite dall'applicazione client, ad esempio un bot di chat.

1. Facoltativamente, aggiungere **prompt di completamento**. Le istruzioni di completamento forniscono percorsi di conversazione aggiuntivi all'applicazione client da presentare all'utente.

1. Selezionare **Salva ed esegui il training** per visualizzare le stime, inclusa la nuova coppia QnA.

## <a name="edit-a-qna-pair"></a>Modificare una coppia QnA

Qualsiasi campo in qualsiasi coppia QnA può essere modificato, indipendentemente dall'origine dati originale. Alcuni campi potrebbero non essere visibili a causa delle impostazioni correnti di **Opzioni di visualizzazione,** disponibili nella barra degli strumenti contestuale.

## <a name="delete-a-qna-pair"></a>Eliminare una coppia domanda/risposta

Per eliminare una coppia domanda/risposta, fare clic sull'icona **Elimina** all'estrema destra della riga. Si tratta di un'operazione definitiva. Non può essere modificata. Prendere in considerazione l'esportazione della KB dalla pagina **Pubblica** prima di eliminare i set.

![Eliminare una coppia domanda/risposta](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Trovare l'ID coppia QnA

Se è necessario trovare l'ID coppia QnA, è possibile trovarlo in due posizioni:

* Passa il mouse sull'icona di eliminazione nella riga della coppia QnA che ti interessa. Il testo al passaggio del mouse include l'ID della coppia QnA.
* Esportare la Knowledge Base. Ogni coppia QnA nella Knowledge Base include l'ID della coppia QnA.

## <a name="add-alternate-questions"></a>Aggiungere domande alternative

Aggiungere domande alternative a una coppia domanda/risposta esistente per aumentare la probabilità di una corrispondenza con le query degli utenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Collegamento di coppie QnA

Il collegamento delle coppie QnA viene fornito con [i prompt di follow-up](multiturn-conversation.md). Si tratta di una connessione logica tra coppie QnA, gestite a livello di Knowledge Base. È possibile modificare le istruzioni di completamento nel portale QnA Maker.

Non è possibile collegare coppie QnA nei metadati della risposta.

## <a name="add-metadata"></a>Aggiungere metadati

Aggiungere i set di metadati selezionando prima **Le opzioni**di visualizzazione , quindi **Mostra metadati**. Verrà visualizzata la colonna dei metadati. Selezionare quindi **+** il segno per aggiungere un set di metadati. Questo set è costituito da una chiave e un valore.

## <a name="save-changes-to-the-qna-pairs"></a>Salvare le modifiche alle coppie QnA

Seleziona Periodicamente **Salva e esegui il training** dopo aver apportato le modifiche per evitare di perdere le modifiche.

![Aggiungere metadati](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare a una Knowledge Base](./collaborate-knowledge-base.md)

* [Gestire le risorse di Azure usate da QnA MakerManage Azure resources used by QnA Maker](set-up-qnamaker-service-azure.md)