---
title: Connettersi all'API di Servizi multimediali di Azure - JavaConnect to Azure Media Services v3 API - Java
description: Questo articolo descrive come connettersi all'API di Servizi multimediali di Azure con Java.This article describes how to connect to Azure Media Services v3 API with Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888496"
---
# <a name="connect-to-media-services-v3-api---java"></a>Connettersi all'API di Servizi multimediali v3 - Java

Questo articolo illustra come connettersi a Azure Media Services v3 Java SDK usando il metodo di accesso dell'entità servizio.

In questo articolo, il codice di Visual Studio viene usato per sviluppare l'app di esempio.

## <a name="prerequisites"></a>Prerequisiti

- Seguire [Scrittura di Java con codice di Visual Studio](https://code.visualstudio.com/docs/java/java-tutorial) per installare:Follow Writing Java with Visual Studio Code to install:

   - JDK
   - Apache Maven
   - Pacchetto estensione Java
- Assicurarsi di `JAVA_HOME` `PATH` impostare e variabili di ambiente.
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire i passaggi nell'argomento [API di accesso.](access-api-cli-how-to.md) Registrare l'ID sottoscrizione, l'ID applicazione (ID client), la chiave di autenticazione (segreto) e l'ID tenant necessari in un passaggio successivo.

Anche rivedere:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gestione di progetti Java nel codice VS](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Esaminare [le convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Creare un progetto Maven

Aprire uno strumento da `cd` riga di comando e in una directory in cui si desidera creare il progetto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando si esegue il `pom.xml` `App.java`comando , vengono creati i file , , e altri. 

## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. In Visual Studio Code aprire la cartella in cui si trova il progetto
1. Trovare e aprire il`pom.xml`
1. Aggiungere le dipendenze necessarie

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Connettersi al client Java

1. Apri `App.java` il `src\main\java\com\azure\ams` file in cui si trova e assicurati che il pacchetto sia incluso nella parte superiore:

    ```java
    package com.azure.ams;
    ```
1. Sotto l'istruzione package aggiungere queste istruzioni import:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Per creare le credenziali di Active Directory necessarie per effettuare richieste, aggiungere il codice seguente al metodo principale della classe App e impostare i valori ottenuti dalle API di [Access:](access-api-cli-how-to.md)
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Eseguire l'app.

## <a name="see-also"></a>Vedere anche

- [Concetti relativi ai Servizi multimediali](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Passaggi successivi

È ora `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` possibile includere e iniziare a manipolare le entità.

Per altri esempi di codice, vedere il repository [di esempi Java SDK.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)
