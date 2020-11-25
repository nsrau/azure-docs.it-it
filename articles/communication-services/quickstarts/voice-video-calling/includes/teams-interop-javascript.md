---
title: 'Avvio rapido: Partecipare a una riunione di Teams'
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ab13e02c9154dbfc1bd28872f1bd6f993019a604
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915510"
---
## <a name="prerequisites"></a>Prerequisiti

- Un'[app per le chiamate di Servizi di comunicazione](../getting-started-with-calling.md) funzionante.
- Una [distribuzione di Teams](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Abilitare l'interoperabilità di Teams

La funzionalità di interoperabilità di Teams è attualmente in fase di anteprima privata. Per abilitare questa funzionalità per la risorsa di Servizi di comunicazione, inviare un messaggio di posta elettronica all'indirizzo [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) con:

1. L'ID della sottoscrizione di Azure che contiene la risorsa di Servizi di comunicazione.
2. L'ID tenant di Teams. Il modo più semplice per ottenere questo dato consiste nell'[ottenere e condividere un collegamento con Teams](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Per usare questa funzionalità, è necessario essere un membro dell'organizzazione proprietaria di entrambe le entità.

## <a name="add-the-teams-ui-controls"></a>Aggiungere i controlli dell'interfaccia utente di Teams

Aggiungere una nuova casella di testo e un nuovo pulsante all'interno del codice HTML. La casella di testo verrà usata per accedere al contesto della riunione di Teams e il pulsante per partecipare alla riunione specificata:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Abilitare i controlli dell'interfaccia utente di Teams

È ora possibile associare il pulsante **Partecipa alla riunione di Teams** al codice che partecipa alla riunione dei Teams specificata:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Ottenere il contesto della riunione

Il contesto di Teams può essere recuperato usando le API Graph. Questa operazione è descritta in dettaglio nella [documentazione di Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

È anche possibile ottenere le informazioni necessarie sulla riunione dall'URL **Partecipa alla riunione** nell'invito.

## <a name="run-the-code"></a>Eseguire il codice

Gli utenti di Webpack possono usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Aprire il browser e passare a http://localhost:8080/. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Screenshot dell'applicazione JavaScript completata.":::

Inserire il contesto di Teams nella casella di testo e premere il pulsante *Partecipa alla riunione di Teams* per partecipare alla riunione di Teams dall'interno dell'applicazione di Servizi di comunicazione.