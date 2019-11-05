---
title: Aggiungere una chit chat a una knowledge base QnA Maker
titleSuffix: Azure Cognitive Services
description: L'aggiunta di una chit chat personale al proprio bot rende più colloquiale e coinvolgente la creazione di una KB. QnA Maker consente di aggiungere facilmente un insieme predefinito delle migliori chit chat nella KB.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 15253e67283d1ad53bb1d4c018987dceaafac6c8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486747"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Aggiungere chit chat a una knowledge base

L’aggiunta di una chit chat al proprio bot lo rende più colloquiale e coinvolgente. La funzione chit chat di QnA Maker consente di aggiungere facilmente un set predefinito delle migliori chit chat nella knowledge base (KB). Può trattarsi di un punto di partenza per la personalità del bot e consentirà di risparmiare tempi e costi di scrittura da zero.  

Questo set di dati presenta circa 100 scenari di Chit-chat con la voce di più persone, ad esempio Professional, friendly e Witty. Scegliere l'utente tipo che rispecchia maggiormente il vocale del bot. Data una query dell'utente, QnA Maker cerca di farla corrispondere alla nota chit chat QnA più vicina.  

Di seguito sono riportati alcuni esempi delle diverse personali. È possibile visualizzare tutti i [set](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) di dati di personalità insieme ai dettagli delle personali.

Per la query utente di `When is your birthday?`, ogni personalità ha una risposta con stile:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalità|Esempio|
|--|--|
|Professional|Age non è effettivamente applicabile all'utente.|
|Gentile|Non ho realmente un tempo.|
|Spiritoso|Sono privo di età.|
|Occuparsi|Non ho un periodo di tempo.|
|Entusiasta|Sono un bot, quindi non ho un periodo di tempo.|
||


## <a name="language-support"></a>Supporto per le lingue

I set di dati Chit-Chat sono supportati nelle seguenti lingue:

|Lingua|
|--|
|Cinese, `zh-cn`|
|Inglese, `en-us`|
|Francese, `fr-fr`|
|Germania, `de-de`|
|Italiano, `it-it`|
|Giapponese, `ja-jp`|
|Coreano, `ko-kr`|
|Portoghese, `pr-br`|
|Spagnolo, `es-es`|


## <a name="add-chit-chat-during-kb-creation"></a>Aggiungere una chit chat durante la creazione di KB
Durante la creazione della knowledge base, dopo aver aggiunto l'URL di origine e i file, è disponibile un'opzione per l'aggiunta di una chit chat. Scegliere la personalità da usare come base di una chit chat. Se non si desidera aggiungere una chit chat o se si dispone già del supporto chit chat nelle origini dati, scegliere **Nessuno.** 

## <a name="add-chit-chat-to-an-existing-kb"></a>Aggiungere una chit chat a una KB esistente
Selezionare la KB e passare alla pagina **Impostazioni**. È disponibile un collegamento a tutti i set di dati chit chat nel formato **.tsv.** Scaricare la personalità desiderata, quindi caricare il file come un file di origine. Assicurarsi di non modificare il formato o i metadati durante il download e caricare il file. 
  
![Aggiungere una chit chat a una KB esistente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Modificare le domande e le risposte alle chit chat
Quando si modifica la KB, si noterà una nuova origine per le chit chat, basata sulla personalità selezionata. È ora possibile aggiungere domande modificate o modificare le risposte, proprio come con qualsiasi altra origine. 

![Modificare QnA chit chat](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Per visualizzare i metadati, selezionare Visualizza **Opzioni** sulla barra degli strumenti e quindi selezionare **Mostra metadati**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Aggiungere ulteriori domande e risposte alle chit chat
È possibile aggiungere nuove QnA chit chat che non sono nel set predefinito. Assicurarsi che non venga duplicata una coppia di QnA già inclusa nel set di chit chat. Quando si aggiunge una nuova QnA chit chat, viene aggiunta al provider di origine **Editoriale.** Per assicurarsi che il classificatore riconosca che si tratta di una chit chat, aggiungere la coppia chiave/valore dei metadati "Editoriale: chit chat", come illustrato nell'immagine seguente:
   
![! [Aggiungere Chit-Chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Eliminare una chit chat da una KB esistente
Selezionare la KB e passare alla pagina **Impostazioni**. L'origine di una chit chat specifica è elencata come un file con il nome della personalità selezionata. È possibile eliminarla come un file di origine.

![Eliminare una chit chat da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare una knowledge base](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Vedere anche 

[Panoramica di QnA Maker](../Overview/overview.md)
