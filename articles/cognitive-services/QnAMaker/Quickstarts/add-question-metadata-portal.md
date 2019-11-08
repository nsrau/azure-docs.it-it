---
title: 'Guida introduttiva: Aggiungere domande e risposte nel portale di QnA Maker'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 92735d8982fb1364d5ebfe0494f5ee51f4302469
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793997"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Guida introduttiva: Aggiungere domande e risposte con il portale di QnA Maker

Dopo aver creato una knowledge base, aggiungere set di domande e risposte con metadati in modo che gli utenti possano trovare la risposta giusta alla propria domanda.

La risposta giusta è una singola risposta, ma la domanda che conduce a quella risposta può essere formulata in molte forme diverse.

Ad esempio, le domande riportate nella tabella seguente sono relative ai limiti dei servizi di Azure, ma ognuna riguarda un servizio di Azure diverso. 

<a name="qna-table"></a>


|Configurazione|Domande|Risposta|Metadata|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Una volta aggiunti i metadati a un set di domande e risposte, l'applicazione client può:

* Richiedere risposte che corrispondono solo a determinati metadati.
* Ricevere tutte le risposte, ma elaborarle successivamente in base ai metadati di ogni risposta.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

* Un servizio QnA Maker
* Una knowledge base creata in quel servizio QnA Maker

Entrambi sono stati creati nella [prima guida di avvio rapido](../how-to/create-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Accedere al portale di QnA Maker

1. Accedere al [portale di QnA Maker](https://www.qnamaker.ai).

1. Selezionare la knowledge base esistente. Se non si ha una knowledge base, tornare alla [guida di avvio rapido precedente](../how-to/create-knowledge-base.md) e completare i passaggi per crearla.

## <a name="add-additional-alternatively-phrased-questions"></a>Aggiungere altre domande formulate in modi alternativi 

La knowledge base corrente, creata nella [guida di avvio rapido precedente](../how-to/create-knowledge-base.md), è costituita da set di domande e risposte sulla risoluzione dei problemi di QnA Maker. Questi set sono stati creati quando l'URL è stato aggiunto alla knowledge base durante il processo di creazione. 

Quando questo URL è stato importato, è stata creata una sola domanda con una sola risposta. 

In questa procedura si aggiungeranno altre domande.

1. Nella pagina **Edit** (Modifica) usare la casella di ricerca sopra i set di domande e risposte per trovare la domanda `How large a knowledge base can I create?`

1. Nella colonna **Question** (Domanda) selezionare **+ Add alternative phrasing** (Aggiungi formulazione alternativa), quindi aggiungere ogni nuova formulazione, fornita nella tabella seguente.

    |Formulazione alternativa|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Selezionare **Save and train** (Salva ed esegui il training) per ripetere il training della knowledge base. 

1. Selezionare **Test**, quindi immettere una domanda simile a una delle nuove formulazioni alternative ma non esattamente uguale:

    `What GB size can a knowledge base be?`

    La risposta corretta viene restituita nel formato Markdown: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Se si seleziona **Inspect** (Esamina) sotto la risposta restituita, si vedrà che più risposte hanno soddisfatto la domanda, ma non con lo stesso livello elevato di attendibilità. 

    Non aggiungere ogni possibile combinazione di formulazioni alternative. Attivare invece l'[apprendimento attivo](../how-to/improve-knowledge-base.md) di QnA Maker, che trova le formulazioni alternative che consentiranno alla knowledge base di soddisfare al meglio le esigenze degli utenti.

1. Selezionare nuovamente **Test** per chiudere la finestra di test.

## <a name="add-metadata-to-filter-the-answers"></a>Aggiungere metadati per filtrare le risposte

L'aggiunta di metadati a un set di domande e risposte consente all'applicazione client di richiedere risposte filtrate. Questo filtro viene applicato prima dell'applicazione [del primo e del secondo classificatore](../concepts/knowledge-base.md#ranker-process).

1. Aggiungere il secondo set di domande e risposte, senza i metadati, dalla [prima tabella di questa guida di avvio rapido](#qna-table), quindi continuare con i passaggi seguenti. 

1. Selezionare **View options** (Opzioni di visualizzazione), quindi **Show metadata** (Mostra metadati). 

1. Per il set di domande e risposte appena aggiunto selezionare **Add metadata tags** (Aggiungi tag di metadati), quindi aggiungere il nome `service` e il valore `search`, `service:search`.

1. Aggiungere altri tag di metadati con il nome `link_in_answer` e il valore `false`, `link_in_answer:false`.

1. Cercare la prima risposta nella tabella, `How large a knowledge base can I create?`. 
1. Aggiungere coppie di metadati per gli stessi due tag di metadati:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Si hanno ora due domande con gli stessi tag di metadati con valori diversi. 

1. Selezionare **Save and train** (Salva ed esegui il training) per ripetere il training della knowledge base. 

1. Selezionare **Publish** (Pubblica) nel menu superiore per passare alla pagina di pubblicazione. 
1. Selezionare il pulsante **Publish** (Pubblica) per pubblicare la knowledge base corrente in un endpoint disponibile per query. 
1. Dopo la pubblicazione della knowledge base, selezionare la scheda **Curl** per visualizzare un esempio di comando cURL usato per generare una risposta dalla knowledge base.
1. Copiare il comando nel Blocco note o in un altro ambiente che consenta di modificarlo. Modificare il nome della risorsa, l'ID della knowledge base e la chiave dell'endpoint:

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Si noti che la domanda è una singola parola, `size`, che può restituire un set di domande e risposte. La matrice `strictFilters` indica di limitare le risposte a quelle con tag `qna_maker`. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. La risposta include solo la risposta che soddisfa i criteri di filtro. 

    La risposta cURL seguente è stata formattata per migliorare la leggibilità:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se è presente un set di domande e risposte che non soddisfa il termine di ricerca ma soddisfa il filtro, non viene restituito. Viene invece restituita la risposta generale `No good match found in KB.`.

    Assicurarsi di mantenere le coppie di nome e valore dei metadati entro i limiti richiesti. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere risposte con Postman](get-answer-from-kb-using-postman.md)