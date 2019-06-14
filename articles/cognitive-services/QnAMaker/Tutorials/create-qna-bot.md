---
title: Bot QnA - Servizio Azure Bot - QnA Maker
titleSuffix: Azure Cognitive Services
description: Crea un chat bot QnA dalla pagina di pubblicazione per una knowledge base esistente. Questo bot utilizza v4 di Bot Framework SDK. Non devi scrivere codice per compilare il bot, tutto il codice viene fornito automaticamente.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: tulasim
ms.openlocfilehash: b3bae01d65685aa9ea7bfc95d1f1454741d37b5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053232"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Esercitazione: Crea un Bot per domande e risposte con Azure v4 servizio Bot

Crea un chat bot QnA dal **pubblica** pagina per una knowledge base esistente. Questo bot utilizza v4 di Bot Framework SDK. Non devi scrivere codice per compilare il bot, tutto il codice viene fornito automaticamente.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un servizio Azure Bot da una knowledge base esistente
> * Avviare una chat con il bot per verificare il funzionamento del codice 

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una knowledge base pubblicata per questa esercitazione. Se non hai uno, seguire i passaggi descritti in [creazione e la risposta da KB](create-publish-query-in-portal.md) esercitazione per creare una knowledge base QnA Maker con domande e risposte.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Creazione di un bot QnA

Creazione di un bot come un'applicazione client per la knowledge base. 

1. Nel portale di QnA Maker, andare alla **pubblica** pagina e pubblicare la knowledge base. Selezionare **creazione di Bot**. 

    ![Nel portale di QnA Maker, passare alla pagina Pubblicazione e pubblicare la knowledge base. Selezionare Creare il bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Consente di aprire il portale di Azure con la configurazione di creazione di bot.

1.  Immettere le impostazioni per creare bot:

    |Impostazione|Value|Scopo|
    |--|--|--|
    |Nome bot|`my-tutorial-kb-bot`|Si tratta del nome di risorsa di Azure per il bot.|
    |Sottoscrizione|Utilizzo generico, vedere.|Come è stato usato per creare le risorse di QnA Maker, selezionare la stessa sottoscrizione.|
    |Gruppo di risorse|`my-tutorial-rg`|Il gruppo di risorse usato per tutti i relativi bot delle risorse di Azure.|
    |Località|`west us`|Percorso della risorsa di Azure del bot.|
    |Piano tariffario|`F0`|Il livello gratuito per il servizio Azure bot.|
    |Nome app|`my-tutorial-kb-bot-app`|Si tratta di un'app web per il tuo bot solo supporto. Questo non deve essere lo stesso nome di app come sta già usando il servizio QnA Maker. Condivisione di app web di QnA Maker con qualsiasi altra risorsa non è supportata.|
    |Java SDK|C#|Questo è il linguaggio di programmazione sottostante usato dal bot framework SDK. Le scelte disponibili sono [ C# ](https://github.com/Microsoft/botbuilder-dotnet) oppure [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |Chiave di autenticazione QnA|**Non modificare**|Questo valore viene inserito automaticamente.|
    |Piano di servizio app/Località|**Non modificare**|Per questa esercitazione, il percorso non è importante.|
    |Archiviazione di Azure|**Non modificare**|Dati di conversazione vengono archiviati nelle tabelle di archiviazione di Azure.|
    |Application Insights|**Non modificare**|La registrazione viene inviata ad Application Insights.|
    |ID app Microsoft|**Non modificare**|Utente di active directory e la password è obbligatorio.|

    ![Creare il bot della knowledge base con queste impostazioni.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Attendere un paio di minuti prima che la notifica di processo di creazione di bot segnala l'esito positivo.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chat con il bot

1. Nel portale di Azure, aprire la nuova risorsa di bot dalla notifica. 

    ![Nel portale di Azure, aprire la nuova risorsa di bot dalla notifica.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Dal **gestione Bot**, selezionare **Test Web chat** e immettere: `How large can my KB be?`. Il bot risponderà con: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Testare il nuovo bot della knowledge base.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Per altre informazioni su Azure Bot, vedere [Usa QnA Maker di rispondere a domande](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Correlati a Bot QnA Maker

* Il bot della Guida di QnA Maker, usato nel portale di QnA Maker, è disponibile come una [esempio di bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![QnA Maker help bot icona è rossa robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Assistenza sanitario BOT](https://docs.microsoft.com/HealthBot/qna_model_howto) Usa QnA Maker come uno dei relativi [modelli di lingua](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato il bot dell'esercitazione, rimuovere il bot nel portale di Azure. 

Se è stato creato un nuovo gruppo di risorse per le risorse del bot, eliminare il gruppo di risorse. 

Se è stato creato un nuovo gruppo di risorse, è necessario trovare le risorse associate al bot. Il modo più semplice è eseguire una ricerca per il nome del bot e app bot. Le risorse di bot includono:

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
