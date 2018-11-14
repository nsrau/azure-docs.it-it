---
title: Modificare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/06/2018
ms.author: tulasim
ms.openlocfilehash: adcefe8fed927aca2533ea811bac56f0b92288de
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279762"
---
# <a name="edit-a-knowledge-base"></a>Modificare una Knowledge Base

QnA Maker consente di gestire il contenuto della Knowledge Base fornendo un'esperienza di modifica facile da usare.

## <a name="edit-your-knowledge-base-content"></a>Modificare il contenuto della Knowledge Base

1.  Selezionare **My Knowledge Bases** (Knowledge Base personali) nella barra di spostamento in alto. 

    È possibile visualizzare tutti i servizi creati o condivisi con l'utente disposti in ordine decrescente in base alla data dell'**ultima modifica**.

    ![Knowledge Base personali](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Selezionare una Knowledge Base per modificarla.
 
3. Fare clic su **Settings** (Impostazioni).

   Qui è possibile modificare il campo obbligatorio Nome del servizio.
  
   È possibile aggiungere nuovi URL per aggiungere nuovo contenuto di domande frequenti sulla Knowledge Base facendo clic sul collegamento **Gestisci Knowledge Base -> " + Aggiungi URL"**.
   
   È possibile eliminare gli URL esistenti facendo clic sull'**icona di eliminazione**.
   
   Se si desidera che la Knowledge base esegua una ricerca per indicizzazione del contenuto più recente degli URL esistenti, spuntare il nome della casella di controllo **"Aggiorna"**, e la Knowledge base verrà aggiornata con il contenuto più recente dell'URL.
   
È possibile aggiungere il documento del file supportato come parte della Knowledge Base, facendo clic su **Gestisci knowledgebase -> '"+ Aggiungi File"**

È anche possibile importare le Knowledge base esistenti facendo clic sul pulsante **"Importa Knowledge base'**. 
   
L'aggiornamento della Knowledge base dipende dal **Piano tariffario della gestione** utilizzato durante la creazione del servizio QnA Maker associato alla knowledgbase. Se necessario, è anche possibile aggiornare il livello di gestione dal portale di Azure.

4. Dopo aver apportato le modifiche alla Knowledge Base, fare clic su **Save and train** (Salva ed esegui training) nell'angolo in alto a destra della pagina per salvare le modifiche in modo permanente.    

    ![Save and train (Salva ed esegui training)](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Se si lascia la pagina prima di fare clic su Save and train (Salva ed esegui training), le modifiche non verranno salvate in modo permanente.

## <a name="add-a-qna-pair"></a>Aggiungere una coppia domanda/risposta

Selezionare **Add QnA pair** (Aggiungi coppia domanda/risposta) per aggiungere una nuova riga alla tabella della Knowledge Base.

![Add QnA pair (Aggiungi coppia domanda/risposta)](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Eliminare una coppia domanda/risposta

Per eliminare una coppia domanda/risposta, fare clic sull'icona **Elimina** all'estrema destra della riga.

![Eliminare una coppia domanda/risposta](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Aggiungere domande alternative

Aggiungere domande alternative a una coppia domanda/risposta esistente per aumentare la probabilità di una corrispondenza con le query degli utenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Aggiungere metadati


Aggiungere coppie di metadati selezionando l'icona del filtro

![Aggiungere metadati](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Salvare ed eseguire periodicamente il training della Knowledge Base dopo aver apportato le modifiche per evitare che vadano perse.

## <a name="manage-large-knowledge-bases"></a>Gestire Knowledge Base di grandi dimensioni

1. Le domande e risposte sono **raggruppate** in base all'origine dati da cui sono state estratte. È possibile espandere o comprimere l'origine dati.
2. È possibile **eseguire ricerche** nella Knowledge Base digitando nella casella di testo nella parte superiore della tabella della Knowledge Base. Premere INVIO per eseguire la ricerca nelle domande, nelle risposte o nei metadati. Fare clic sull'icona X per rimuovere il filtro di ricerca.
3. La **paginazione** consente di gestire Knowledge Base di grandi dimensioni

    ![Ricerca, paginazione, gruppo](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="delete-knowledge-bases"></a>Eliminare le knowledge base

L'eliminazione di una knowledge base (KB) è un'operazione definitiva. Non può essere modificata. Prima di eliminare una knowledge base è necessario esportarla dalla pagina **Impostazioni** del portale di QnA Maker. 

Se si condivide la KB con i [collaboratori](collaborate-knowledge-base.md) e quindi la si elimina, tutti gli utenti perdono l'accesso alla knowledge base. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare a una Knowledge Base](./collaborate-knowledge-base.md)
