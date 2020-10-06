---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376447"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Node.js](https://nodejs.org/en/download/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md). Per questa guida di avvio rapido sarà necessario registrare l'**endpoint** della risorsa.
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-web-application"></a>Creare una nuova applicazione Web

Aprire prima di tutto il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

Usare un editor di testo per creare un file denominato **start-chat.js** nella directory radice del progetto. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

### <a name="install-the-packages"></a>Installare i pacchetti

Usare il comando `npm install` per installare le librerie client di Servizi di comunicazione seguenti per JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Questa guida di avvio rapido usa webpack per aggregare gli asset dell'applicazione. Eseguire il comando seguente per installare i pacchetti webpack, webpack-cli e webpack-dev-server npm ed elencarli come dipendenze di sviluppo in **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Creare un file **index.html** nella directory radice del progetto. Questo file verrà usato come modello per aggiungere la funzionalità di chat usando la libreria client di chat di comunicazione di Azure per JavaScript.

Il codice è il seguente:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Creare un file nella directory radice del progetto denominato **client.js** per contenere la logica dell'applicazione per questa guida di avvio rapido. 

### <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat nell'app Web, si useranno l'endpoint di Servizi di comunicazione e il token di accesso generato nell'ambito dei passaggi preliminari. I token di accesso utente consentono di creare applicazioni client che eseguono l'autenticazione direttamente in Servizi di comunicazione di Azure. Dopo aver generato questi token nel server, passarli a un dispositivo client. È necessario usare la classe `AzureCommunicationUserCredential` dalla libreria `Common client library` per passare il token al client di chat.

Creare un file **client.js** nella directory radice del progetto. Questo file verrà usato per aggiungere la funzionalità di chat usando la libreria client di chat di comunicazione di Azure per JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Sostituire **ENDPOINT** con quello creato prima nella documentazione [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md).
Sostituire **USER_ACCESS_TOKEN** con un token generato in base alla documentazione [Token di accesso utente](../../access-tokens.md).
Aggiungere questo codice al file **client.js**.


### <a name="run-the-code"></a>Eseguire il codice
Usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Aprire il browser e passare a http://localhost:8080/.
Nella console degli strumenti di sviluppo nel browser dovrebbe essere visualizzato quanto segue:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Modello a oggetti 
Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura, sottoscrivere eventi di chat. |


## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `createThread` per creare un thread di chat.

`createThreadRequest` viene usato per descrivere la richiesta di thread:

- Usare `topic` per specificare un argomento per questa chat. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `UpdateThread`. 
- Usare `members` per elencare i membri da aggiungere al thread di chat.

Al termine delle operazioni, il metodo `createChatThread` restituisce il valore `threadId` usato per eseguire operazioni sul thread di chat appena creato, ad esempio l'aggiunta di membri al thread di chat, l'invio di messaggi, l'eliminazione di messaggi e così via.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Sostituire **USER_ID_FOR_JACK** e **USER_ID_FOR_GEETA** con gli ID utente ottenuti dal passaggio precedente (Creare utenti e generare [token di accesso utente](../../access-tokens.md))

Quando si aggiorna la scheda del browser, nella console dovrebbe essere visualizzato quanto segue
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Il metodo `getChatThreadClient` restituisce un `chatThreadClient` per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere membri, inviare messaggi e così via. threadId è l'ID univoco del thread di chat esistente.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Aggiungere questo codice al posto del commento `<CREATE CHAT THREAD CLIENT>` in **client.js**, aggiornare la scheda del browser e controllare la console. Verrà visualizzato quanto segue:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il metodo `sendMessage` per inviare un messaggio di chat al thread appena creato, identificato da threadId.

`sendMessageRequest` descrive i campi obbligatori della richiesta di messaggio di chat:

- Usare `content` per fornire il contenuto del messaggio di chat.

`sendMessageOptions` descrive i campi facoltativi della richiesta di messaggio di chat:

- Usare `priority` per specificare il livello di priorità del messaggio di chat, ad esempio 'Normal' o 'High'. Questa proprietà può essere usata per avere un indicatore dell'interfaccia utente per l'utente destinatario nell'app, per attirare l'attenzione sul messaggio o eseguire logica di business personalizzata.   
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente.

Il `sendChatMessageResult` di risposta contiene un "id", ovvero l'ID univoco del messaggio.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Aggiungere questo codice al posto del commento `<SEND MESSAGE TO A CHAT THREAD>` in **client.js**, aggiornare la scheda del browser e controllare la console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

Con la segnalazione in tempo reale, è possibile sottoscrivere l'ascolto dei nuovi messaggi in arrivo e aggiornare i messaggi correnti in memoria di conseguenza. Servizi di comunicazione Azure supporta un [elenco di eventi che è possibile sottoscrivere](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Aggiungere questo codice al posto del commento `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` in **client.js**.
Aggiornare la scheda del browser. Nella console verrà visualizzato un messaggio `Notification chatMessageReceived`.

In alternativa, è possibile recuperare i messaggi di chat eseguendo il polling del metodo `listMessages` a intervalli specificati. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Aggiungere questo codice al posto del commento `<LIST MESSAGES IN A CHAT THREAD>` in **client.js**.
Aggiornare la scheda. Nella console dovrebbe essere disponibile l'elenco dei messaggi inviati in questo thread di chat.


`listMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `updateMessage` e `deleteMessage`.
Per i messaggi eliminati, `chatMessage.deletedOn` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.editedOn` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.createdOn` e tale ora può essere usata per ordinare i messaggi.

`listMessages` restituisce tipi diversi di messaggi che possono essere identificati da `chatMessage.type`. Questi tipi sono:

- `Text`: messaggio di chat normale inviato da un membro del thread.

- `ThreadActivity/TopicUpdate`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ThreadActivity/AddMember`: messaggio di sistema che indica che uno o più membri sono stati aggiunti al thread di chat.

- `ThreadActivity/RemoveMember`: messaggio di sistema che indica che un membro è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Aggiungere un utente come membro al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per inviare messaggi al thread di chat e aggiungere o rimuovere altri membri. Prima di chiamare il metodo `addMembers`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

`addMembersRequest` descrive l'oggetto richiesta in cui `members` elenca i membri da aggiungere al thread di chat.
- `user`, obbligatoria, è l'utente di comunicazione da aggiungere al thread di chat.
- `displayName`, facoltativa, è il nome visualizzato per il membro del thread.
- `shareHistoryTime`, facoltativa, è l'ora da cui la cronologia della chat viene condivisa con il membro. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente all'aggiunta del membro, impostarla sulla data corrente. Per condividere una cronologia parziale, impostarla su una data a scelta.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Sostituire **NEW_MEMBER_USER_ID** con un [nuovo ID utente](../../access-tokens.md). Aggiungere questo codice al posto del commento `<ADD NEW MEMBER TO THREAD>` in **client.js**

## <a name="list-users-in-a-chat-thread"></a>Elencare gli utenti in un thread di chat
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Aggiungere questo codice al posto del commento `<LIST MEMBERS IN A THREAD>` in **client.js**, aggiornare la scheda del browser e controllare la console. Verranno visualizzate informazioni sugli utenti in un thread.

## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un membro, è possibile rimuovere membri da un thread di chat. Per eseguire la rimozione, è necessario tenere traccia degli ID dei membri aggiunti.

Usare il metodo `removeMember` in cui `member` è l'utente di comunicazione da rimuovere dal thread.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Sostituire **MEMBER_ID** con un ID utente usato nel passaggio precedente (<NEW_MEMBER_USER_ID>).
Aggiungere questo codice al posto del commento `<REMOVE MEMBER FROM THREAD>` in **client.js**.
