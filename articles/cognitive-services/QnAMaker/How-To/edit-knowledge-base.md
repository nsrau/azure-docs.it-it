---
title: Modificare una knowledge base - QnA Maker
description: QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131649"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Modificare i set QnA nella Knowledge Base

QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.

I set QnA vengono aggiunti da un'origine dati, ad esempio un file o un URL, oppure vengono aggiunti come origine editoriale. Una fonte editoriale indica che il set QnA è stato aggiunto manualmente nel portale QnA. Tutti i set QnA sono disponibili per la modifica.

## <a name="add-an-editorial-qna-set"></a>Aggiungere un set QnA editoriale
1. Accedere al [portale QnA](https://www.qnamaker.ai/), quindi selezionare la Knowledge Base a cui aggiungere il set QnA.
1. Nella pagina **MODIFICA** della Knowledge Base selezionare **Aggiungi set QnA** per aggiungere un nuovo set QnA.

1. Nella nuova riga del set QnA aggiungere i campi **Domanda** e **Risposta** necessari. Gli altri campi sono facoltativi. Tutti i campi possono essere modificati in qualsiasi momento.

1. Facoltativamente, aggiungere **formulazioni alternative.** La formulazione alternativa è qualsiasi forma della domanda che è significativamente diversa dalla domanda originale, ma dovrebbe fornire la stessa risposta.

    Quando la Knowledge Base viene pubblicata e l'apprendimento attivo è attivato, QnA Maker raccoglie scelte di formulazione alternative da accettare. Queste scelte vengono selezionate per aumentare l'accuratezza della stima.

1. Facoltativamente, aggiungere **metadati**. Per visualizzare i metadati, selezionate **Opzioni di visualizzazione** nel menu di scelta rapida. I metadati forniscono filtri alle risposte fornite dall'applicazione client, ad esempio un bot di chat.

1. Facoltativamente, aggiungere **prompt di completamento**. Le istruzioni di completamento forniscono percorsi di conversazione aggiuntivi all'applicazione client da presentare all'utente.

1. Selezionare **Salva ed esegui il training** per visualizzare le stime, incluso il nuovo set QnA.

## <a name="edit-a-qna-set"></a>Modificare un set QnA

Qualsiasi campo in qualsiasi set QnA può essere modificato, indipendentemente dall'origine dati originale. Alcuni campi potrebbero non essere visibili a causa delle impostazioni correnti di **Opzioni di visualizzazione,** disponibili nella barra degli strumenti contestuale.

## <a name="delete-a-qna-set"></a>Eliminare un set QnADelete a QnA set

Per eliminare una coppia domanda/risposta, fare clic sull'icona **Elimina** all'estrema destra della riga. Si tratta di un'operazione definitiva. Non può essere modificata. Prendere in considerazione l'esportazione della KB dalla pagina **Pubblica** prima di eliminare i set.

![Elimina set QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Trovare l'ID del set QnA

Se è necessario trovare l'ID set QnA, è possibile trovarlo in due posizioni:

* Passa il mouse sull'icona di eliminazione nella riga del set QnA che ti interessa. Il testo al passaggio del mouse include l'ID del set QnA.
* Esportare la Knowledge Base. Ogni set QnA nella Knowledge Base include l'ID del set QnA.

## <a name="add-alternate-questions"></a>Aggiungere domande alternative

Aggiungere domande alternative a un set QnA esistente per migliorare la probabilità di corrispondenza con una query utente.

![Aggiungere domande alternative](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Collegamento di set QnA

Il collegamento dei set QnA viene fornito con [le istruzioni di completamento.](multiturn-conversation.md) Si tratta di una connessione logica tra i set QnA, gestiti a livello di Knowledge Base. È possibile modificare le istruzioni di completamento nel portale QnA Maker.

Non è possibile collegare i set QnA nei metadati della risposta.

## <a name="add-metadata"></a>Aggiungere metadati

Aggiungere i set di metadati selezionando prima **Le opzioni**di visualizzazione , quindi **Mostra metadati**. Verrà visualizzata la colonna dei metadati. Selezionare quindi **+** il segno per aggiungere un set di metadati. Questo set è costituito da una chiave e un valore.

## <a name="save-changes-to-the-qna-sets"></a>Salvare le modifiche ai set QnA

Seleziona Periodicamente **Salva e esegui il training** dopo aver apportato le modifiche per evitare di perdere le modifiche.

![Aggiungere metadati](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare a una Knowledge Base](./collaborate-knowledge-base.md)

* [Gestire le risorse di Azure usate da QnA MakerManage Azure resources used by QnA Maker](set-up-qnamaker-service-azure.md)