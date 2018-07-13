---
title: Come creare un'applicazione dello Strumento di apprendimento di conversazioni usando Node.js - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come creare un'applicazione dello Strumento di apprendimento di conversazioni usando Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377676"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Creare un'applicazione dello Strumento di apprendimento di conversazioni usando Node.js

Lo Strumento di apprendimento di conversazioni riduce la complessità della compilazione di bot. Abilita un flusso di lavoro di sviluppo ibrido che consente all'apprendimento automatico e al codice scritto a mano di ridurre la quantità di codice necessaria per scrivere bot. Alcune parti fisse dell'applicazione, come verificare se l'utente ha effettuato l'accesso o effettuare una richiesta API per controllare l'inventario del negozio, possono ancora essere codificate. Tuttavia, altri cambiamenti nello stato e nella selezione delle azioni possono essere appresi dalle finestre di dialogo di esempio fornite dall'esperto o dallo sviluppatore del dominio.

## <a name="invitation-required"></a>Invito necessario

*Per accedere al Progetto Strumento di apprendimento di conversazioni è necessario un invito.*

Il Progetto Strumento di apprendimento di conversazioni è costituito da un SDK aggiunto al bot e da un servizio cloud al quale l'SDK accede per l'apprendimento automatico.  Attualmente, l'accesso al servizio cloud del Progetto Strumento di apprendimento di conversazioni richiede un invito.  Se non è ancora stato ricevuto un invito, [richiedere un invito](https://aka.ms/conversation-learner-request-invite).  Senza un invito non sarà possibile accedere all'API del cloud.

## <a name="prerequisites"></a>prerequisiti

- Node 8.5.0 o versione successiva e npm 5.3.0 o versione successiva. Installare da [https://nodejs.org](https://nodejs.org).
  
- Chiave di creazione di LUIS:

  1. Accedere a [http://www.luis.ai](http://www.luis.ai).

  2. Fare clic sul nome in alto a destra, quindi scegliere "impostazioni"

  3. La chiave di creazione viene visualizzata nella pagina risultante

  La chiave di creazione di LUIS serve per 2 ruoli.  In primo luogo, servirà come chiave di creazione dello Strumento di apprendimento di conversazioni.  In secondo luogo, lo Strumento di apprendimento di conversazioni usa LUIS per l'estrazione di entità. La chiave di creazione di LUIS viene usata per creare modelli di LUIS per proprio conto.

- Web browser Google Chrome. Installare da [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- git. Installare da [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Installare da [https://code.visualstudio.com/](https://code.visualstudio.com/). Nota: questo procedimento non è obbligatorio ma è consigliato.

## <a name="quick-start"></a>Avvio rapido 

1. Installare e compilare:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Durante `npm install` è possibile ignorare questo errore: `gyp ERR! stack Error: Can't find Python executable`

2. Configurare:

   Creare un file denominato `.env` nella directory `cl-bot-01`.  Il contenuto del file dovrebbe essere:

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Avviare un bot:

    ```
    npm start
    ```

    Questo procedimento esegue il bot vuoto generico in `cl-bot-01/src/app.ts`.

3. Eseguire l'interfaccia utente dello Strumento di apprendimento di conversazioni:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Aprire il browser alla pagina http://localhost:5050 

Si sta ora usando lo Strumento di apprendimento di conversazioni ed è possibile creare e migliorare un modello dello Strumento di apprendimento di conversazioni.  

> [!NOTE]
> Al momento del lancio, il Progetto Strumento di apprendimento di conversazioni è disponibile su invito.  Se http://localhost:5050 mostra un errore HTTP `403`, l'account non è stato invitato.  [Richiedere un invito](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Esercitazioni, demo e passaggio tra bot

Le istruzioni sopra riportate hanno avviato il bot vuoto generico.  Per eseguire invece un'esercitazione o bot demo:

1. Se l'interfaccia utente Web dello Strumento di apprendimento di conversazioni è aperta, tornare all'elenco delle app su http://localhost:5050/home.
    
2. Se un altro bot (ad esempio `npm start` o `npm run demo-pizza`) è in esecuzione, arrestarlo.  Non è necessario arrestare il processo dell'interfaccia utente o chiudere il Web browser.

3. Eseguire un bot demo dalla riga di comando (passaggio 2 in alto).  Le demo includono:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Se non è già aperta, passare all'interfaccia utente Web dello Strumento di apprendimento di conversazioni in Chrome caricando http://localhost:5050/home. 

5. Fare clic su "Import tutorials" (Importa esercitazioni). Questa operazione va eseguita solo una volta.  Questa procedura richiederà circa un minuto e copierà i modelli dello Strumento di apprendimento di conversazioni per tutte le esercitazioni sull'account dello Strumento di apprendimento di conversazioni.

6. Fare clic sul modello demo nell'interfaccia utente dello Strumento di apprendimento di conversazioni che corrisponde alla demo avviata.

I file di origine per le demo si trovano in `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Creare un bot che include il codice di back-end

1. Se l'interfaccia utente Web dello Strumento di apprendimento di conversazioni è aperta, tornare all'elenco delle app su http://localhost:5050/home.
    
2. Se un bot (ad esempio `npm run demo-pizza`) è in esecuzione, arrestarlo.  Non è necessario arrestare il processo dell'interfaccia utente o chiudere il Web browser.

3. Se si desidera, modificare il codice in `cl-bot-01/src/app.ts`.

4. Ricompilare e riavviare il bot:

    ```bash    
    npm run build
    npm start
    ```

5. Se non è già aperta, passare all'interfaccia utente Web dello Strumento di apprendimento di conversazioni in Chrome caricando http://localhost:5050/home. 

6. Creare una nuova applicazione dello Strumento di apprendimento di conversazioni nell'interfaccia utente e avviare l'insegnamento.

7. Per apportare modifiche al codice in `cl-bot-01/src/app.ts`, ripetere i passaggi precedenti, partendo dal passaggio 2.

## <a name="vscode"></a>VSCode

In VSCode ci sono configurazioni di esecuzione per ogni demo e per il "Bot vuoto" in `cl-bot-01/src/app.ts`.  Aprire la cartella `cl-bot-01` in VSCode.

## <a name="advanced-configuration"></a>Configurazione avanzata

C'è un file `.env.example` modello che mostra quali variabili di ambiente è possibile impostare per configurare gli esempi.

È possibile regolare queste porte per evitare conflitti tra gli altri servizi in esecuzione nel computer aggiungendo un file `.env` alla radice del progetto:

```bash
cp .env.example .env
```

Usa la configurazione standard, che consente di eseguire il bot in locale e iniziare a usare lo Strumento di apprendimento conversazioni.  (In un secondo momento, per distribuire il bot sul Bot Framework sarà necessario apportare qualche modifica a questo file.)

## <a name="support"></a>Supporto

- Contrassegnare le domande su [Stack Overflow](https://stackoverflow.com) con "microsoft cognitive"
- Richiedere una funzionalità nella [pagina Suggerimenti degli utenti](https://aka.ms/conversation-learner-uservoice)
- Aprire un problema nell'[archivio github](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Contributo

Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft). Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare [opencode@microsoft.com](mailto:opencode@microsoft.com) per eventuali altre domande o commenti.

## <a name="source-repositories"></a>Archivi di origine

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Hello world](./tutorials/1-hello-world.md)
