---
title: Bot QnA - Servizio Azure Bot - QnA Maker
titleSuffix: Azure Cognitive Services
description: Creare un chatbot QnA dalla pagina Publish (Pubblica) per una knowledge base esistente. Questo bot usa Bot Framework SDK v4. Non è necessario scrivere codice per creare il bot, tutto il codice è già disponibile.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698000"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Esercitazione: Creare un bot QnA con il servizio Azure Bot v4

Creare un chatbot QnA dalla pagina **Publish** (Pubblica) per una knowledge base esistente. Questo bot usa Bot Framework SDK v4. Non è necessario scrivere codice per creare il bot, tutto il codice è già disponibile.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un servizio Azure Bot da una knowledge base esistente
> * Avviare una chat con il bot per verificare il funzionamento del codice 

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una knowledge base pubblicata per questa esercitazione. Se non è disponibile, seguire i passaggi dell'esercitazione [Creare e rispondere da una knowledge base](create-publish-query-in-portal.md) per creare una knowledge base di QnA Maker con domande e risposte.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Creazione di un bot QnA

Creare un bot come applicazione client per la knowledge base. 

1. Nel portale di QnA Maker passare alla pagina **Publish** (Pubblica) e pubblicare la knowledge base. Selezionare **Create Bot** (Crea bot). 

    ![Nel portale di QnA Maker, passare alla pagina Pubblicazione e pubblicare la knowledge base. Selezionare Creare il bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Verrà aperto il portale di Azure con la configurazione per la creazione del bot.

1.  Immettere le impostazioni per creare il bot:

    |Impostazione|Valore|Scopo|
    |--|--|--|
    |Nome bot|`my-tutorial-kb-bot`|Nome della risorsa di Azure per il bot.|
    |Subscription|Vedere lo scopo.|Selezionare la stessa sottoscrizione usata per creare le risorse di QnA Maker.|
    |Resource group|`my-tutorial-rg`|Gruppo di risorse usato per tutte le risorse di Azure correlate al bot.|
    |Location|`west us`|Località in cui si trova la risorsa di Azure del bot.|
    |Piano tariffario|`F0`|Il livello di servizio gratuito per il servizio Azure Bot.|
    |Nome app|`my-tutorial-kb-bot-app`|Nome dell'app Web usata solo per supportare il bot. Non deve corrispondere al nome dell'app usata per il servizio QnA Maker. La condivisione dell'app Web di QnA Maker con altre risorse non è supportata.|
    |Linguaggio SDK|C#|Linguaggio di programmazione sottostante usato da Bot Framework SDK. È possibile scegliere [C#](https://github.com/Microsoft/botbuilder-dotnet) o [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |Chiave di autenticazione QnA|**Non modificare**|Questo valore viene impostato automaticamente.|
    |Piano di servizio app/Località|**Non modificare**|Per questa esercitazione la località è importante.|
    |Archiviazione di Azure|**Non modificare**|I dati delle conversazioni vengono archiviati nelle tabelle di Archiviazione di Azure.|
    |Application Insights|**Non modificare**|I dati dei log vengono inviati ad Application Insights.|
    |ID app Microsoft|**Non modificare**|È necessario specificare il nome utente e la password di Active Directory.|

    ![Creare il bot della knowledge base con queste impostazioni.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Attendere alcuni minuti finché non viene notificato l'esito positivo del processo di creazione del bot.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chat con il bot

1. Nel portale di Azure aprire la nuova risorsa bot dalla schermata di notifica. 

    ![Nel portale di Azure aprire la nuova risorsa bot dalla schermata di notifica.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. In **Bot management** (Gestione bot) selezionare **Test in Web Chat** (Testa nella chat Web) e immettere: `How large can my KB be?`. Il bot risponderà nel modo seguente: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Testare il nuovo bot della knowledge base.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Per altre informazioni su Azure Bot, vedere [Usare QnA Maker per rispondere alle domande](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato il bot dell'esercitazione, rimuovere il bot nel portale di Azure. 

Se è stato creato un nuovo gruppo di risorse per le risorse del bot, eliminare il gruppo. 

Se non è stato creato un nuovo gruppo di risorse, è necessario trovare le risorse associate al bot. Il modo più semplice consiste nell'eseguire una ricerca in base al nome del bot e della relativa app. Le risorse bot includono:

* Piano di servizio app
* Il servizio di ricerca
* Il Servizio cognitivo
* Il servizio app
* Facoltativamente, può anche includere il servizio Application Insights e spazio di archiviazione per i dati di Application Insights


## <a name="related-to-qna-maker-bots"></a>Bot correlati a QnA Maker

* Il bot della Guida di QnA Maker, usato nel portale di QnA Maker, è disponibile come [esempio di bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![Robot rosso usato come icona del bot della Guida di QnA Maker](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* I [bot per i servizi sanitari](https://docs.microsoft.com/HealthBot/qna_model_howto) usano QnA Maker come uno dei [modelli di lingua](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Concetto: knowledge base](../concepts/knowledge-base.md)

## <a name="see-also"></a>Vedere anche

- [Gestire la knowledge base](https://qnamaker.ai)
- [Abilitare i bot nei diversi canali](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
