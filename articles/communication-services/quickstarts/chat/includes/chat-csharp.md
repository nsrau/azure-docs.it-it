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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816683"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:
- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md). Per questa guida di avvio rapido sarà necessario registrare l'**endpoint** della risorsa.
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `ChatQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Installare la libreria client di chat per la comunicazione di Azure per .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per C#.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat, si useranno l'endpoint di Servizi di comunicazione e il token di accesso generato nell'ambito dei passaggi preliminari. È necessario usare la classe `CommunicationIdentityClient` dalla libreria client `Administration` per creare un utente e generare un token da passare al client di chat. Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `createChatThread` per creare un thread di chat.
- Usare `topic` per specificare un argomento per questa chat. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `UpdateThread`.
- Usare la proprietà `members` per passare un elenco di oggetti `ChatThreadMember` da aggiungere al thread di chat. L'oggetto `ChatThreadMember` viene inizializzato con un oggetto `CommunicationUser`. Per ottenere un oggetto `CommunicationUser`, sarà necessario passare un ID di accesso creato seguendo le istruzioni in [Creare un utente](../../access-tokens.md#create-an-identity).

Il `chatThreadClient` di risposta viene usato per eseguire operazioni sul thread di chat creato: aggiunta di membri al thread di chat, invio di un messaggio, eliminazione di un messaggio e così via. Contiene l'attributo `Id` che rappresenta l'ID univoco del thread di chat. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat
Il metodo `GetChatThreadClient` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere membri, inviare messaggi e così via. threadId è l'ID univoco del thread di chat esistente.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il metodo `SendMessage` per inviare un messaggio a un thread identificato da threadId.

- Usare `content` per fornire il contenuto del messaggio di chat, obbligatorio.
- Usare `priority` per specificare il livello di priorità del messaggio, ad esempio 'Normal' o 'High'. Se non viene specificato, verrà usato 'Normal'.
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente. Se non specificato, verrà usato un nome vuoto.

`SendChatMessageResult` è la risposta restituita dall'invio di un messaggio contiene un ID, ovvero l'ID univoco del messaggio.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `GetMessages` sul client del thread di chat a intervalli specificati.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` accetta il parametro facoltativo `DateTimeOffset`. Se si specifica tale offset, si riceveranno i messaggi ricevuti, aggiornati o eliminati dopo di esso. Si noti che verranno restituiti anche i messaggi ricevuti prima della data/ora di offset ma modificati o rimossi dopo tale data/ora.

`GetMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `UpdateMessage` e `DeleteMessage`. Per i messaggi eliminati, `chatMessage.DeletedOn` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.EditedOn` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.CreatedOn` e tale ora può essere usata per ordinare i messaggi.

`GetMessages` restituisce tipi diversi di messaggi che possono essere identificati da `chatMessage.Type`. Questi tipi sono:

- `Text`: messaggio di chat normale inviato da un membro del thread.

- `ThreadActivity/TopicUpdate`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ThreadActivity/AddMember`: messaggio di sistema che indica che uno o più membri sono stati aggiunti al thread di chat.

- `ThreadActivity/DeleteMember`: messaggio di sistema che indica che un membro è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Aggiornare un messaggio

È possibile aggiornare un messaggio che è già stato inviato richiamando `UpdateMessage` su `ChatThreadClient`.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Eliminazione di un messaggio

È possibile eliminare un messaggio richiamando `DeleteMessage` su `ChatThreadClient`.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Aggiungere un utente come membro al thread di chat

Dopo aver creato un thread, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per inviare messaggi al thread e aggiungere o rimuovere altri membri. Prima di chiamare `AddMembers`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare il metodo `AddMembers` per aggiungere membri di thread al thread identificato da threadId.

 - Usare `members` per elencare i membri da aggiungere al thread di chat.
 - `User`, obbligatoria, è l'identità che si ottiene per questo nuovo utente.
 - `DisplayName`, facoltativa, è il nome visualizzato per il membro del thread.
 - `ShareHistoryTime`, facoltativa, è l'ora da cui la cronologia della chat viene condivisa con il membro. Per condividere la cronologia dall'inizio del thread di chat, impostarla su DateTime.MinValue. Per non condividere alcuna cronologia precedente all'aggiunta del membro, impostarla sulla data corrente. Per condividere una cronologia parziale, impostarla su un punto nel tempo tra la creazione del thread e l'ora corrente.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un utente a un thread, è possibile rimuovere utenti da un thread di chat. A tale scopo, è necessario tenere traccia dell'identità (CommunicationUser) dei membri aggiunti.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```
