---
title: Come distribuire un bot di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come distribuire un bot di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 77cc998227d996a6e52b1b5629204da5dc735ede
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376177"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Come distribuire un bot di Conversation Learner

Questo documento illustra come distribuire un bot di Conversation Learner, in locale o in Azure.

## <a name="prerequisite-determine-the-application-id"></a>Prerequisito: Determinare l'ID applicazione 

Per eseguire un bot all'esterno dell'interfaccia utente di Conversation Learner, è necessario impostare l'ID applicazione di Conversation Learner che verrà usato dal bot, ossia l'ID del modello di apprendimento automatico nel cloud Conversation Learner.  Quando il bot viene eseguito tramite l'interfaccia utente di Conversation Learner, invece, l'ID applicazione viene scelto dall'interfaccia utente.  

Ecco come ottenere l'ID applicazione:

1. Avviare il bot e l'interfaccia utente di Conversation Learner.  Per istruzioni complete, vedere la guida introduttiva. Di seguito è riportato un riepilogo.

    In una finestra di comando:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    In un'altra finestra di comando:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Aprire il browser alla pagina http://localhost:5050 

3. Fare clic sull'applicazione di Conversation Learner di cui si vuole ottenere l'ID.

4. Fare clic su "Settings" (Impostazioni) nella barra di spostamento a sinistra.

5. Nella parte superiore della pagina viene visualizzato il GUID "App ID" (ID app).

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Opzione 1: Distribuzione di un bot di Conversation Learner per l'esecuzione in locale

Questa opzione prevede la distribuzione di un bot nel computer locale e illustra come è possibile accedervi con Bot Framework Emulator.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Configurare il bot per l'accesso all'esterno dell'interfaccia utente di Conversation Learner

Quando si esegue un bot in locale, aggiungere l'ID applicazione al file con estensione `.env` del bot:

    ```
    CONVERSATION_LEARNER_APP_ID=<YOUR_APP_ID>
    ```

Avviare quindi il bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Il bot viene ora eseguito in locale  ed è possibile accedervi con Bot Framework Emulator.

### <a name="download-and-install-the-emulator"></a>Scaricare e installare l'emulatore

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Connettere l'emulatore al bot

1. Nella casella "Enter your endpoint URL" (Immettere URL endpoint) in alto a sinistra nell'emulatore immettere `http://127.0.0.1:3978/api/messages`.  Lasciare vuoti gli altri campi e fare clic su "Connect" (Connetti).

2. Viene così attivata la conversazione con il bot.

## <a name="option-2-deploy-to-azure"></a>Opzione 2: Distribuzione in Azure

Pubblicare il bot di Conversation Learner così come si pubblicherebbe qualsiasi altro bot. A livello generale, si carica il codice in un sito Web ospitato, si impostano i valori di configurazione appropriati e quindi si registra il bot in vari canali. Istruzioni dettagliate sono disponibili in questo video, che illustra come pubblicare il bot con il servizio Azure Bot.

Quando il bot è distribuito e in esecuzione, è possibile connettervi diversi canali, come Facebook, Teams, Skype e così via, usando una registrazione di canale di Azure Bot. Per la documentazione relativa a tale processo, vedere https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Di seguito sono riportate istruzioni dettagliate per la distribuzione di un bot di Conversation Learner in Azure.  Queste istruzioni presuppongono che l'origine del bot sia disponibile in un'origine basata sul cloud come VSTS, GitHub, BitBucket o OneDrive e configurano il bot per la distribuzione continua.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com

2. Creare una nuova risorsa "Web App Bot" (Bot app Web). 

    1. Assegnare un nome al bot
    2. Fare clic su "Bot Template" (Modello bot), scegliere "Node.js" e "Basic" e quindi fare clic sul pulsante "Select" (Seleziona)
    3. Fare clic su "Create" (Crea) per creare il bot per app Web
    4. Attendere che la risorsa bot per app Web venga creata

3. Nel portale di Azure modificare la risorsa bot per app Web appena creata.

    1. Fare clic sull'elemento di spostamento "Application Settings" (Impostazioni applicazione) a sinistra
    1. Scorrere verso il basso fino alla sezione "App Settings" (Impostazioni app)
    2. Aggiungere queste impostazioni:

        Variabile di ambiente | Valore
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_APP_ID      | GUID dell'ID applicazione, ottenuto dalle impostazioni dell'app nell'interfaccia utente di Conversation Learner
        LUIS_AUTHORING_KEY               | Chiave di creazione di LUIS per l'app
    
    4. Fare clic su "Save" (Salva) nella parte superiore della pagina
    5. Aprire l'elemento di spostamento "Build" (Compilazione) a sinistra
    6. Fare clic su "Configure continuous deployment" (Configura distribuzione continua) 
    7. Fare clic sull'icona "Setup" (Configurazione) sotto le distribuzioni
    8. Fare clic su "Required Settings" (Impostazioni obbligatorie)
    9. Selezionare l'origine in cui è disponibile il codice del bot e configurarla.
