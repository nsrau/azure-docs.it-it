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
ms.openlocfilehash: 69b3e900973b1be484b76764847767b9e74d9998
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816557"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.


## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando e passare alla directory in cui creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Si noterà che l'obiettivo 'generate' ha creato una directory con lo stesso nome di artifactId. In questa directory, `src/main/java directory` contiene il codice sorgente del progetto, la directory `src/test/java` contiene l'origine di test e il file pom.xml è il modello a oggetti del progetto o [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Aggiornare il file POM dell'applicazione per usare Java 8 o versione successiva:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>Aggiungere i riferimenti al pacchetto per la libreria client di chat

Nel file POM fare riferimento al pacchetto `azure-communication-chat` con le API di chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

Per l'autenticazione, il client deve fare riferimento al pacchetto `azure-communication-common`:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per Java.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatAsyncClient | Questa classe è necessaria per la funzionalità di chat asincrona. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |
| ChatThreadAsyncClient | Questa classe è necessaria per la funzionalità di thread di chat asincrona. È possibile ottenere un'istanza tramite ChatAsyncClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat
Per creare un client di chat, si useranno l''endpoint di Servizi di comunicazione e il token di accesso generato nell'ambito dei passaggi preliminari. I token di accesso utente consentono di creare applicazioni client che eseguono l'autenticazione direttamente in Servizi di comunicazione di Azure. Dopo aver generato questi token nel server, passarli a un dispositivo client. È necessario usare la classe CommunicationUserCredential dalla libreria client comune per passare il token al client di chat. 

Quando si aggiungono le istruzioni di importazione, assicurarsi di aggiungere solo le importazioni dagli spazi dei nomi com.azure.communication.chat e com.azure.communication.chat.models e non dallo spazio dei nomi com.azure.communication.chat.implementation. Nel file App.java generato tramite Maven, è possibile usare il codice seguente per iniziare:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `createChatThread` per creare un thread di chat.
`createChatThreadOptions` viene usato per descrivere la richiesta di thread.

- Usare `topic` per specificare un argomento per questa chat. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `UpdateThread`.
- Usare `members` per elencare i membri del thread da aggiungere al thread. `ChatThreadMember` accetta l'utente creato nella guida di avvio rapido [Token di accesso utente](../../access-tokens.md).

Il `chatThreadClient` di risposta viene usato per eseguire operazioni sul thread di chat creato: aggiunta di membri al thread di chat, invio di un messaggio, eliminazione di un messaggio e così via. Contiene una proprietà `chatThreadId` che rappresenta l'ID univoco del thread di chat. La proprietà è accessibile dal metodo pubblico .getChatThreadId().

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il metodo `sendMessage` per inviare un messaggio al thread appena creato, identificato da chatThreadId.
`sendChatMessageOptions` consente di descrivere la richiesta del messaggio di chat.

- Usare `content` per fornire il contenuto del messaggio di chat.
- Usare `priority` per specificare il livello di priorità del messaggio di chat, ad esempio 'Normal' o 'High'. Questa proprietà può essere usata per avere un indicatore dell'interfaccia utente per l'utente destinatario nell'app, per attirare l'attenzione sul messaggio o eseguire logica di business personalizzata.
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente.

Il `sendChatMessageResult` di risposta contiene un `id`, ovvero l'ID univoco del messaggio.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Il metodo `getChatThreadClient` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere membri, inviare messaggi e così via. `chatThreadId` è l'ID univoco del thread di chat esistente.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `listMessages` sul client del thread di chat a intervalli specificati.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando .editMessage() e .deleteMessage(). Per i messaggi eliminati, `chatMessage.getDeletedOn()` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.getEditedOn()` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.getCreatedOn()` e tale ora può essere usata per ordinare i messaggi.

`listMessages` restituisce tipi diversi di messaggi che possono essere identificati da `chatMessage.getType()`. Questi tipi sono:

- `Text`: messaggio di chat normale inviato da un membro del thread.

- `ThreadActivity/TopicUpdate`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ThreadActivity/AddMember`: messaggio di sistema che indica che uno o più membri sono stati aggiunti al thread di chat.

- `ThreadActivity/DeleteMember`: messaggio di sistema che indica che un membro è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Aggiungere un utente come membro al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per inviare messaggi al thread di chat e aggiungere o rimuovere altri membri. Per iniziare, sarà necessario ottenere un nuovo token di accesso e un'identità per tale utente. Prima di chiamare il metodo addMembers, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare il metodo `addMembers` per aggiungere membri di thread al thread identificato da threadId.

- Usare `members` per elencare i membri da aggiungere al thread di chat.
- `user`, obbligatoria, è il CommunicationUser creato da CommunicationIdentityClient nella guida di avvio rapido [Token di accesso utente](../../access-tokens.md).
- `display_name`, facoltativa, è il nome visualizzato per il membro del thread.
- `share_history_time`, facoltativa, è l'ora da cui la cronologia della chat viene condivisa con il membro. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente all'aggiunta del membro, impostarla sulla data corrente. Per condividere una cronologia parziale, impostarla sulla data richiesta.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un utente a un thread, è possibile rimuovere utenti da un thread di chat. A tale scopo, è necessario tenere traccia delle identità utente dei membri aggiunti.

Usare `removeMember`, dove `user` è il CommunicationUser creato.

```Java
chatThreadClient.removeMember(user);
```

## <a name="run-the-code"></a>Eseguire il codice

Passare alla directory contenente il file *pom.xml* e compilare il progetto tramite il comando `mvn` seguente.

```console
mvn compile
```

Compilare il pacchetto.

```console
mvn package
```

Eseguire il comando `mvn` seguente per eseguire l'app.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
