---
title: 'Esercitazione: Creare un bot di domande frequenti con il servizio Azure Bot'
description: Questa esercitazione illustra come creare un bot domande frequenti senza codice con QnA Maker e il servizio Azure Bot.
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: 0ec3b381c4361234b29717164b6b718aff0ae294
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652311"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Esercitazione: Creare un bot di domande frequenti con il servizio Azure Bot
Creare un bot domande frequenti con QnA Maker e il [servizio Azure Bot](https://azure.microsoft.com/en-us/services/bot-service/) senza codice.

In questa esercitazione verranno illustrate le procedure per:

<!-- green checkmark -->
> [!div class="checklist"]
> * Collegare una knowledge base di QnA Maker a un servizio Azure Bot
> * Distribuire il bot
> * Chattare con il bot in una chat Web
> * Attivare il bot nei canali supportati

## <a name="create-and-publish-a-knowledge-base"></a>Creare e pubblicare una knowledge base

Seguire la [guida di avvio rapido](../Quickstarts/create-publish-knowledge-base.md) per creare una knowledge base. Dopo aver pubblicato la knowledge base, si accederà alla pagina seguente.

![Screenshot del completamento della pubblicazione](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Creare un bot

Dopo la pubblicazione è possibile creare un bot dalla pagina **Publish** (Pubblica):

* È possibile creare rapidamente diversi bot, che indirzzano tutti alla stessa knowledge base per aree o piani tariffari differenti per i singoli bot.
* Se si vuole solo un bot per la knowledge base, usare il collegamento **View all your bots on the Azure portal** (Visualizza tutti i bot nel portale di Azure) per visualizzare un elenco dei bot correnti.

Quando si apportano modifiche alla knowledge base e si ripete la pubblicazione, non sono necessarie ulteriori azioni con il bot. È già configurato per interagire con la knowledge base e funziona con tutte le successive modifiche a essa apportate. Ogni volta che si pubblica una knowledge base, tutti i bot connessi vengono aggiornati automaticamente.

1. Nella pagina **Pubblicazione** del portale di QnA Maker, selezionare **Creare il bot**. Questo pulsante viene visualizzato solo dopo la pubblicazione della knowledge base.

    ![Schermata della creazione di un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Viene visualizzata una nuova scheda del browser per il portale di Azure, con la pagina di creazione del servizio Azure Bot. Configurare il servizio Azure Bot. Il bot e QnA Maker possono condividere il piano di servizio dell'app Web, ma non l'app Web. Ciò significa che il **nome app** del bot deve essere diverso da quello del servizio QnA Maker.

    * **Cosa fare**
        * Cambiare l'handle del bot, se non è univoco.
        * Selezionare il linguaggio dell'SDK. Una volta creato il bot, è possibile scaricare il codice nell'ambiente di sviluppo locale e continuare il processo di sviluppo.
    * **Cosa non fare**
        * Cambiare le impostazioni seguenti nel portale di Azure quando si crea il bot. Sono già popolate per la knowledge base esistente:
           * Chiave di autenticazione QnA
           * Piano di servizio app e località


1. Dopo aver creato il bot, aprire la risorsa **servizio Bot**.
1. In **Bot Management** (Gestione bot) selezionare **Test in Web Chat** (Testa nella chat Web).
1. Quando viene visualizzato il prompt della chat **Type your message** (Digitare il messaggio), immettere:

    `Azure services?`

    Il chatbot risponde con una risposta della knowledge base.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Immettere una query utente nella chat Web di test.":::
1. Attivare il bot in altri [canali supportati](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true).
