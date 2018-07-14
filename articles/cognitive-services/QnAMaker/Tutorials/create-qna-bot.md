---
title: Creazione di un bot QnA con il servizio di Azure Bot - Servizi cognitivi di Azure | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: fc430bf3aa7cad279d7a93bb6892aa19abee3378
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109270"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Creare un bot QnA con il servizio Azure Bot
Questo tutorial illustra in modo guidato la compilazione di un bot QnA con il servizio Azure Bot sul portale di Azure.

## <a name="prerequisite"></a>Prerequisito
Prima della compilazione, seguire i passaggi in [Creare una knowledge base](../How-To/create-knowledge-base.md) per creare un servizio QnA Maker con domande e risposte.

Il bot risponde alle domande della knowledge base create dall'utente, tramite il QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Creazione di un bot QnA
1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** nel pannello menu, quindi scegliere **Visualizza tutto**.

    ![Creazione di servizi bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Nella casella di ricerca, cercare **App Web Bot**.

    ![Selezione del servizio bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Nel **pannello Servizio bot** fornire le informazioni richieste e selezionare **Crea**. In questo modo viene creato e distribuito il servizio bot con QnAMakerDialog in Azure.

    - Impostare il **nome dell'app** sul nome del bot. Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, ad esempio mynotesbot.azurewebsites.net.
    - Selezionare la sottoscrizione, il gruppo di risorse, il piano di servizio dell'app e la posizione.
    - Selezionare **Domanda e risposta**. (Node.js o C#) per il campo Bot del modello.
    - Selezionare la casella di controllo di conferma per le note legali. Il contenuto delle note legali si trova sotto la casella di controllo.

        ![Selezione del servizio bot](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Confermare che il servizio robot è stato distribuito.

    - Selezionare le **notifiche** (l'icona a forma di campanello visibile nella parte superiore del portale di Azure). La notifica cambierà da **Distribuzione iniziata** a **Distribuzione completata**.
    - Dopo che la notifica è cambiata in **Distribuzione completata**, selezionare **Vai alla risorsa** in quella notifica.

## <a name="chat-with-the-bot"></a>Chat con il bot
Selezionando **Vai alla risorsa** consente di passare al pannello della risorsa del bot.

Dopo aver completato la registrazione del bot, fare clic su **Testa nella chat Web** per aprire il riquadro Chat Web. Digitare "ciao" nella chat Web.

![Chat Web con bot QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Il bot risponde con "Per favore imposta QnAKnowledgebaseId e QnASubscriptionKey nelle Impostazioni app". Scopri come ottenerli in https://aka.ms/qnaabssetup". Questa risposta conferma che il bot QnA ha ricevuto il messaggio, ma non c'è ancora una knowledge base QnA Maker associata. Ciò sarà effettuato nel passaggio successivo.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Connessione della knowledge base QnA Maker al bot

1. Aprire **Impostazioni applicazione** e modificare i campi **QnAKnowledgebaseId**, **QnAAuthKey** e **QnAEndpointHostName** affinché contengano i valori della knowledge base QnA Maker.

    ![app settings](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Ottenere l'ID della knowledge base, l'url host e la chiave di endpoint dalla scheda delle impostazioni della knowledge base in https://qnamaker.ai.
    - Accesso al [QnA Maker](https://qnamaker.ai)
    - Accedere alla knowledge base
    - Fare clic sulla scheda **Impostazioni**
    - **Pubblicare** la knowledge base, se non è stato già fatto

    ![Valori QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Se si desidera connettere la versione di anteprima della knowledge base con il bot QnA, impostare il valore di **Ocp-Apim-sottoscrizione-Key** su **QnAAuthKey**. Lasciare il **QnAEndpointHostName** vuoto.

## <a name="test-the-bot"></a>Test del bot
Nel portale di Azure, fare clic su **Testa nella chat Web** per testare il bot. 

![Bot QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Il bot QnA risponde ora dalla knowledge base.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Integrare di QnA Maker e Language Understanding](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Vedere anche 

- [Gestire la knowledge base](https://qnamaker.ai)
- [Abilitare i bot nei diversi canali](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
