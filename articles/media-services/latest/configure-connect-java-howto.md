---
title: Connettersi all'API v3 di servizi multimediali di Azure - Java
description: Informazioni su come connettersi all'API di servizi multimediali v3 con Java.
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
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: b7ee54c852ce3332415b69ca6105b472dab0ab8a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480266"
---
# <a name="connect-to-media-services-v3-api---java"></a>Connettersi all'API servizi multimediali di v3 - Java

Questo articolo illustra come connettersi a servizi multimediali di Azure v3 Java SDK con il segno dell'entità servizio nel metodo.

In questo articolo viene usato Visual Studio Code per sviluppare app di esempio.

## <a name="prerequisites"></a>Prerequisiti

- Seguire [Writing Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) installare:

   - JDK
   - Apache Maven
   - Java Extension Pack
- Verificare di aver impostato `JAVA_HOME` e `PATH` variabili di ambiente.
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account servizi multimediali.
- Seguire i passaggi nel [accesso API](access-api-cli-how-to.md) argomento. Registrare l'ID sottoscrizione, ID applicazione (ID client), la chiave di autenticazione (segreto) e l'ID tenant è necessario in un passaggio successivo.

Vedere anche:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gestione dei progetti Java in Visual Studio Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Creare un progetto Maven

Aprire uno strumento da riga di comando e `cd` in una directory in cui si desidera creare il progetto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando si esegue il comando, il `pom.xml`, `App.java`, e vengono creati altri file. 

## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. In Visual Studio Code, aprire la cartella in cui il progetto è
1. Trovare e aprire la `pom.xml`
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

## <a name="connect-to-the-java-client"></a>Connettersi a Java client

1. Aprire il `App.java` del file in `src\main\java\com\azure\ams` e assicurarsi che il pacchetto è incluso nella parte superiore:

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
1. Per creare le credenziali di Active Directory che è necessario per effettuare richieste, aggiungere il codice seguente al metodo main della classe App e impostare i valori che è stato ottenuto da [accesso API](access-api-cli-how-to.md):
   
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
- [SDK per Java](https://aka.ms/ams-v3-java-sdk)
- [Riferimento Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile includere `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e avviare la modifica delle entità.
