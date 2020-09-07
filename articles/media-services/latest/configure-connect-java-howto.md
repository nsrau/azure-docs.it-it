---
title: Connettersi alle API di Servizi multimediali di Azure v3 - Java
description: Questo argomento illustra come connettersi alle API di Servizi multimediali di Azure v3 con Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 0f099a1b807cb860aaeda95a442cfdd7fd3c2869
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297383"
---
# <a name="connect-to-media-services-v3-api---java"></a>Connettersi alle API di Servizi multimediali v3 - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come connettersi all'SDK Java di Servizi multimediali di Azure v3 con il metodo di firma dell'entità servizio.

In questo articolo viene usato il Visual Studio Code per sviluppare l'app di esempio.

## <a name="prerequisites"></a>Prerequisiti

- Seguire l'articolo [Scrivere codice Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) per installare:

   - JDK
   - Apache Maven
   - Java Extension Pack
- Assicurarsi di impostare le variabili di ambiente `JAVA_HOME` e `PATH`.
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e quello dell'account di Servizi multimediali.
- Seguire la procedura descritta nell'argomento [Accedere alle API](./access-api-howto.md). Registrare l'ID sottoscrizione, l'ID applicazione (ID client) e la chiave di autenticazione (segreto), oltre all'ID tenant che sarà necessario in un passaggio successivo.

Vedere anche:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java Project Management in VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Creare un progetto Maven

Aprire uno strumento da riga di comando e `cd` in una directory in cui creare il progetto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando si esegue il comando, vengono creati i file `pom.xml`, `App.java` e altri. 

## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. In Visual Studio Code aprire la cartella in cui si trova il progetto
1. Trovare e aprire il file `pom.xml`
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

1. Aprire il file `App.java` in `src\main\java\com\azure\ams` e assicurarsi che il pacchetto sia incluso all'inizio:

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
1. Per creare le credenziali di Active Directory necessarie per effettuare richieste, aggiungere il codice seguente al metodo main della classe App e impostare i valori ottenuti seguendo l'articolo [Accedere alle API](./access-api-howto.md):
   
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

È ora possibile includere `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e iniziare a modificare le entità.

Per altri esempi di codice, vedere il repository di [esempi di Java SDK](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/).
