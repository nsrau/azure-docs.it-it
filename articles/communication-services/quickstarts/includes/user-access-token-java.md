---
title: includere file
description: includere file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: de578ec286a8232ee8d4e259b2f37fb76101f7a5
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506236"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando. Passare alla directory in cui creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

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
import com.azure.communication.administration.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con l'endpoint e la chiave di accesso della risorsa. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessKey))
    .httpClient(httpClient)
    .buildClient();
```

È possibile inizializzare il client con qualsiasi client HTTP personalizzato che implementi l'interfaccia `com.azure.core.http.HttpClient`. Il codice precedente illustra l'uso del [client HTTP Azure Core Netty](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) fornito da `azure-core`.

## <a name="create-an-identity"></a>Creare un'identità

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `createUser` per creare una nuova voce nella directory con `Id` univoco. Archiviare l'identità ricevuta con il mapping agli utenti dell'applicazione. Ad esempio, archiviarla nel database del server applicazioni. L'identità sarà necessaria in seguito per emettere i token di accesso.

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>Risolvere i problemi dei token di accesso

Usare il metodo `issueToken` per emettere un token di accesso per un'identità esistente di Servizi di comunicazione. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md). È possibile creare una nuova istanza del parametro `user` in base alla rappresentazione di stringa dell'identità di Servizi di comunicazione di Azure.

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String identityId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + identityId + ": " + token);
```

I token di accesso sono credenziali di breve durata che devono essere riemesse. In caso contrario, si potrebbe verificare un'interruzione nell'esperienza degli utenti dell'applicazione. La proprietà della risposta `expiresAt` indica la durata del token di accesso.

## <a name="refresh-access-tokens"></a>Aggiornare i token di accesso

Per aggiornare un token di accesso, usare l'oggetto `CommunicationUser` per riemetterlo:

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Revocare i token di accesso

In alcuni casi, è possibile revocare esplicitamente i token di accesso, ad esempio quando l'utente di un'applicazione cambia la password che usa per l'autenticazione con il servizio. Il metodo `revokeTokens` invalida tutti i token di accesso attivi che sono stati emessi all'identità.

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nRevoked access tokens for the user with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>Eliminare un'identità

L'eliminazione di un'identità determina la revoca di tutti i token di accesso attivi e ne impedisce l'emissione per l'identità. Comporta inoltre la rimozione di tutto il contenuto persistente associato all'identità.

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nSuccessfully deleted the identity with ID: " + identity.getId());
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
