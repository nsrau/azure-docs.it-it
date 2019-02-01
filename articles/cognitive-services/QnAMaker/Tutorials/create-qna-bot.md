---
title: Bot QnA - Servizio Azure Bot - QnA Maker
titleSuffix: Azure Cognitive Services
description: Questo tutorial illustra in modo guidato la compilazione di un bot QnA con il servizio Azure Bot v3 sul portale di Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker`
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 428aea5775c1777f38d7ea563cfe3af700c27f02
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211832"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Esercitazione: Creare un bot QnA con il servizio Azure Bot v3

Questa esercitazione illustra in modo guidato la compilazione di un bot QnA con il servizio Azure Bot v3 sul [portale di Azure](https://portal.azure.com) senza scrivere alcun codice. La connessione di una knowledge base pubblicata (KB) per un bot è facile come modificare le impostazioni dell'applicazione bot. 

> [!Note] 
> Questo argomento si riferisce alla versione 3 dell'SDK di Bot. La versione 4 è disponibile [qui](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un servizio Azure Bot con il modello di QnA Maker
> * Avviare una chat con il bot per verificare il funzionamento del codice 
> * Connettere la Knowledge Base pubblicata al bot
> * Testare il bot con una domanda

Per questo articolo, è possibile usare il [servizio](../how-to/set-up-qnamaker-service-azure.md) QnA Maker gratuito.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una knowledge base pubblicata per questa esercitazione. Se non è disponibile, seguire i passaggi in [Creare una knowledge base](../How-To/create-knowledge-base.md) per creare un servizio QnA Maker con domande e risposte.

## <a name="create-a-qna-bot"></a>Creazione di un bot QnA

1. Nel portale di Azure fare clic su **Crea una risorsa**.

    ![Creazione di servizi bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Nella casella di ricerca, cercare **App Web Bot**.

    ![Selezione del servizio bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    - Impostare il **nome dell'app** sul nome del bot. Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, ad esempio mynotesbot.azurewebsites.net.
    - Selezionare la sottoscrizione, il gruppo di risorse, il piano di servizio dell'app e la posizione.

4. Per usare i modelli v3, selezionare la versione di SDK di **SDK v3** e la lingua SDK del **C#** oppure **Node. js**.

    ![impostazioni di sdk di bot](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Selezionare il modello **domande e risposte** per il campo modello di Bot, quindi salvare le impostazioni del modello selezionando **seleziona**.

    ![salvare una selezione di modelli del servizio bot](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Rivedere le impostazioni e quindi selezionare **Crea**. Il servizio bot viene creato e distribuito in Azure.

    ![Creare il bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Confermare che il servizio bot è stato distribuito.

    - Selezionare le **notifiche** (l'icona a forma di campanello visibile nella parte superiore del portale di Azure). La notifica cambierà da **Distribuzione iniziata** a **Distribuzione completata**.
    - Dopo che la notifica è cambiata in **Distribuzione completata**, selezionare **Vai alla risorsa** in quella notifica.

## <a name="chat-with-the-bot"></a>Chat con il bot

Selezionando **Vai alla risorsa** consente di passare alla risorsa del bot.

Selezionare **Test in Web Chat** (Testa nella chat Web) per aprire il riquadro Web Chat (Chat Web). Digitare "Ciao"nella chat Web.

![Chat Web con bot QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Il bot risponde con "Per favore imposta QnAKnowledgebaseId e QnASubscriptionKey nelle Impostazioni app". Questa risposta conferma che il bot QnA ha ricevuto il messaggio, ma non c'è ancora una knowledge base QnA Maker associata. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Connessione della knowledge base QnA Maker al bot

1. Aprire **Impostazioni applicazione** e modificare i campi **QnAKnowledgebaseId**, **QnAAuthKey** e **QnAEndpointHostName** affinché contengano i valori della knowledge base QnA Maker.

    ![app settings](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Ottenere l'ID della knowledge base, l'url host e la chiave di endpoint dalla scheda delle impostazioni della knowledge base nel portale QnA Maker.

    - Accedere al [QnA Maker](https://qnamaker.ai)
    - Accedere alla knowledge base
    - Fare clic sulla scheda **Impostazioni**
    - **Pubblicare** la knowledge base, se non è stato già fatto

    ![Valori QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Se si desidera connettere la versione di anteprima della knowledge base con il bot QnA, impostare il valore di **Ocp-Apim-sottoscrizione-Key** su **QnAAuthKey**. Lasciare il **QnAEndpointHostName** vuoto.

## <a name="test-the-bot"></a>Testare il bot

Nel portale di Azure selezionare **Test in Web Chat** (Testa nella chat Web) per testare il bot. 

![Bot QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Il bot QnA risponde dalla knowledge base.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato il bot dell'esercitazione, rimuovere il bot nel portale di Azure. I servizi bot includono:

* Piano di servizio app
* Il servizio di ricerca
* Il Servizio cognitivo
* Il servizio app
* Facoltativamente, può anche includere il servizio Application Insights e spazio di archiviazione per i dati di Application Insights

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Concetto: knowledge base](../concepts/knowledge-base.md)

## <a name="see-also"></a>Vedere anche 

- [Gestire la knowledge base](https://qnamaker.ai)
- [Abilitare i bot nei diversi canali](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
