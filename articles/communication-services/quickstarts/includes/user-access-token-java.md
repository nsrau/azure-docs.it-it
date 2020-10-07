---
title: includere file
description: includere file
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779430"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando e passare alla directory in cui si vuole creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Si noterà che l'attività 'generate' ha creato una directory con lo stesso nome di `artifactId`. In questa directory, la directory src/main/java contiene il codice sorgente del progetto, la directory `src/test/java directory` contiene l'origine di test e il file `pom.xml` è il modello a oggetti del progetto o POM.

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file **pom.xml** nell'editor di testo. Aggiungere l'elemento di dipendenza seguente al gruppo di dipendenze.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Passare alla directory */src/main/java/com/communication/quickstart*
1. Aprire il file *App.java* nell'editor
1. Sostituire l'istruzione `System.out.println("Hello world!");`
1. Aggiungere le direttive `import`

Per iniziare, usare il codice seguente:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con l'endpoint e la chiave di accesso della risorsa. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

È possibile inizializzare il client con qualsiasi client HTTP personalizzato che implementi l'interfaccia `com.azure.core.http.HttpClient`. Il codice precedente illustra l'uso del [client HTTP Azure Core Netty](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) fornito da `azure-core`.

## <a name="create-a-user"></a>Creare un utente

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `createUser` per creare una nuova voce nella directory con `Id` univoco. È necessario mantenere un mapping tra gli utenti dell'applicazione e le identità generate da Servizi di comunicazione, ad esempio archiviandoli nel database del server applicazioni.

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Emettere i token di accesso utente

Usare il metodo `issueToken` per emettere un token di accesso per un utente di Servizi di comunicazione. Se non viene fornito il parametro `user` facoltativo, verrà creato un nuovo utente e restituito con il token.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

I token di accesso utente sono credenziali di breve durata che devono essere riemesse per impedire che si verifichino interruzioni del servizio per gli utenti. La proprietà di risposta `expiresAt` indica la durata del token.

## <a name="revoke-user-access-tokens"></a>Revocare i token di accesso utente

In alcuni casi può essere necessario revocare i token di accesso utente in modo esplicito, ad esempio quando un utente modifica la password usata per eseguire l'autenticazione al servizio. Questa operazione usa il metodo `revokeTokens` per invalidare tutti i token di accesso di un utente.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Eliminare un utente

L'eliminazione di un utente determina la revoca di tutti i token attivi e impedisce l'emissione di token successivi per le identità. Viene anche rimosso tutto il contenuto persistente associato all'utente.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
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
