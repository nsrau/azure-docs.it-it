---
title: includere file
description: includere file
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 76aae596c145c736ae75e65f7f72fdbdcead5919
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779735"
---
Introduzione a Servizi di comunicazione di Azure, con l'invio di messaggi SMS tramite la libreria client SMS di Servizi di comunicazione di Azure per Java.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire `mvn -v` per verificare se Maven è installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando e passare alla directory in cui creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

L'obiettivo 'generate' creerà una directory con lo stesso nome di artifactId. In questa directory la directory **src/main/java** contiene il codice sorgente del progetto, la **directory src/test/java** contiene l'origine di test e il file **pom.xml** è il modello a oggetti del progetto (POM).

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file **pom.xml** nell'editor di testo. Aggiungere l'elemento di dipendenza seguente al gruppo di dipendenze.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aggiungere la dipendenza `azure-core-http-netty` al file **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Aprire **/src/main/java/com/communication/quickstart/App.java** in un editor di testo, aggiungere le direttive import e rimuovere l'istruzione `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client SMS di Servizi di comunicazione di Azure per Java.

| Nome                                                             | Descrizione                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Questa classe crea l'oggetto SmsClient. Specificare l'endpoint, le credenziali e un client HTTP. |
| SmsClient                    | Questa classe è necessaria per tutte le funzionalità SMS. Viene usata per inviare messaggi SMS.                |
| SendSmsResponse               | Questa classe contiene la risposta del servizio SMS.                                          |
| CommunicationClientCredential | Questa classe gestisce le richieste di firma.                                                            |
| PhoneNumber                   | Questa classe include informazioni sul numero di telefono

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `SmsClient` con la stringa di connessione. Il codice seguente recupera le stringhe dell'endpoint e delle credenziali per la risorsa dalle variabili di ambiente denominate `COMMUNICATION_SERVICES_ENDPOINT_STRING` e `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (Credential corrisponde a `Key` del portale di Azure. Vedere come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

È possibile inizializzare il client con qualsiasi client HTTP personalizzato che implementi l'interfaccia `com.azure.core.http.HttpClient`. Il codice precedente illustra l'uso del [client HTTP Azure Core Netty](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) fornito da `azure-core`.

## <a name="send-an-sms-message"></a>Inviare SMS

Inviare un messaggio SMS chiamando il metodo sendMessage. Aggiungere questo codice alla fine del metodo `main`:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

È necessario sostituire `<leased-phone-number>` con un numero di telefono abilitato per gli SMS associato alla risorsa di Servizi di comunicazione e `<to-phone-number>` con il numero di telefono a cui inviare un messaggio.

Il parametro `enableDeliveryReport` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Eseguire il codice

Passare alla directory contenente il file **pom.xml** e compilare il progetto tramite il comando `mvn`.

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
