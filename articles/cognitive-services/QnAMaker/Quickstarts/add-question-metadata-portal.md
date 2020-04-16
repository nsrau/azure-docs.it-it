---
title: 'Guida introduttiva: Aggiungere domande e risposte nel portale di QnA Maker'
description: Questo argomento di avvio rapido illustra come aggiungere set di domande e risposte con metadati in modo che gli utenti possano trovare la risposta corretta per la loro domanda.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756694"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Guida introduttiva: Aggiungere domande e risposte con il portale di QnA Maker

Una volta creata una knowledge base, aggiungere i set di domande e risposte con i metadati per filtrare la risposta. Le domande riportate nella tabella seguente sono relative ai limiti dei servizi di Azure, ma ognuna riguarda un servizio di Azure diverso.

<a name="qna-table"></a>

|Set|Domande|Risposta|Metadati|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Una volta aggiunti i metadati a una coppia di domande e risposte, l'applicazione client può:

* Richiedere risposte che corrispondono solo a determinati metadati.
* Ricevere tutte le risposte, ma elaborarle successivamente in base ai metadati di ogni risposta.


## <a name="prerequisites"></a>Prerequisiti

* Completare gli [argomenti di avvio rapido precedenti](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Accedere al portale di QnA Maker

1. Accedere al [portale di QnA Maker](https://www.qnamaker.ai).

1. Selezionare la knowledge base esistente creata nell'[argomento di avvio rapido precedente](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Aggiungere altre domande formulate in modi alternativi

Nella knowledge base corrente sono presenti le coppie di domande e risposte per la risoluzione dei problemi di QnA Maker. Questi set sono stati creati quando l'URL è stato aggiunto alla knowledge base durante il processo di creazione.

Quando questo URL è stato importato, è stata creata una sola domanda con una sola risposta. In questa procedura si aggiungeranno altre domande.

1. Nella pagina **Edit** (Modifica) usare la casella di ricerca sopra i set di domande e risposte per trovare la domanda `How large a knowledge base can I create?`

1. Nella colonna **Question** (Domanda) selezionare **+ Add alternative phrasing** (Aggiungi formulazione alternativa), quindi aggiungere ogni nuova formulazione, fornita nella tabella seguente.

    |Formulazione alternativa|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selezionare **Save and train** (Salva ed esegui il training) per ripetere il training della knowledge base.

1. Selezionare **Test**, quindi immettere una domanda simile a una delle nuove formulazioni alternative ma non esattamente uguale:

    `What GB size can a knowledge base be?`

    La risposta corretta viene restituita nel formato Markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Se si seleziona **Inspect** (Esamina) sotto la risposta restituita, si vedrà che più risposte hanno soddisfatto la domanda, ma non con lo stesso livello elevato di attendibilità.

    Non aggiungere ogni possibile combinazione di formulazioni alternative. Quando si attiva l'[apprendimento attivo](../how-to/improve-knowledge-base.md) di QnA Maker, vengono trovate le formulazioni alternative che consentiranno alla knowledge base di soddisfare al meglio le esigenze degli utenti.

1. Selezionare nuovamente **Test** per chiudere la finestra di test.

## <a name="add-metadata-to-filter-the-answers"></a>Aggiungere metadati per filtrare le risposte

L'aggiunta di metadati a un set di domande e risposte consente all'applicazione client di richiedere risposte filtrate. Questo filtro viene applicato prima dell'applicazione [del primo e del secondo classificatore](../concepts/query-knowledge-base.md#ranker-process).

1. Aggiungere il secondo set di domande e risposte, senza i metadati, dalla [prima tabella di questa guida di avvio rapido](#qna-table), quindi continuare con i passaggi seguenti.

1. Selezionare **View options** (Opzioni di visualizzazione), quindi **Show metadata** (Mostra metadati).

1. Per la coppia di domande e risposte appena aggiunta selezionare **Add metadata tags** (Aggiungi tag di metadati), quindi aggiungere il nome `service` e il valore `search`. L'aspetto sarà simile al seguente: `service:search`.

1. Aggiungere altri tag di metadati con il nome `link_in_answer` e il valore `false`. L'aspetto sarà simile al seguente: `link_in_answer:false`.

1. Cercare la prima risposta nella tabella, `How large a knowledge base can I create?`.

1. Aggiungere coppie di metadati per gli stessi due tag di metadati:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Si hanno ora due domande con gli stessi tag di metadati con valori diversi.

1. Selezionare **Save and train** (Salva ed esegui il training) per ripetere il training della knowledge base.

1. Selezionare **Publish** (Pubblica) nel menu superiore per passare alla pagina di pubblicazione.
1. Selezionare il pulsante **Publish** (Pubblica) per pubblicare la knowledge base corrente nell'endpoint.
1. Dopo la pubblicazione della knowledge base, passare all'argomento di avvio rapido successivo per informazioni su come generare una risposta dalla knowledge base.

## <a name="what-did-you-accomplish"></a>Cosa si è ottenuto?

È stata modificata la knowledge base per supportare più domande e sono state fornite coppie nome/valore per supportare l'applicazione di filtri durante la ricerca della risposta principale o la postelaborazione, dopo la restituzione della risposta o delle risposte.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continua con l'argomento di avvio rapido successivo, eliminare le risorse QnA Maker e Bot Framework nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere risposte con Postman o cURL](get-answer-from-knowledge-base-using-url-tool.md)