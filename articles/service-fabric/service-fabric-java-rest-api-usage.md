---
title: API client Java di Azure Service Fabric | Microsoft Docs
description: Generare e usare le API client Java di Service Fabric con la specifica API REST client di Service Fabric
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: a6cb5ffe6c21c6eda9eb8b7f610952e92707a6bd
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483472"
---
# <a name="azure-service-fabric-java-client-apis"></a>API client Java di Azure Service Fabric

Le API client di Service Fabric consentono di distribuire e gestire applicazioni e contenitori basati su microservizi in un cluster di Service Fabric in Azure, in locale, in un computer di sviluppo locale o in un altro cloud. Questo articolo descrive come generare e usare le API client Java di Service Fabric oltre alle API REST client di Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Generare il codice client usando AutoRest

[AutoRest](https://github.com/Azure/autorest) è uno strumento che genera librerie client per l'accesso ai servizi Web RESTful. L'input per AutoRest è una specifica che descrive l'API REST usando il formato della specifica OpenAPI. Le [API REST client di Service Fabric](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) seguono questa specifica.

Seguire la procedura indicata sotto per generare il codice client Java di Service Fabric usando lo strumento AutoRest.

1. Installare nodejs e NPM nella macchina virtuale

    Se si usa Linux:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Se si usa Mac OS X:
    ```bash
    brew install node
    ```

2. Installare AutoRest usando NPM.
    ```bash
    npm install -g autorest
    ```

3. Creare una copia tramite fork del repository [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs), clonarlo nel computer locale e passare alla posizione clonata dal terminale del computer.


4. Passare alla posizione indicata di seguito nel repository clonato.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se la versione del cluster non è 6.0.*, passare alla directory appropriata nella cartella stable.
    >   

5. Eseguire il comando autorest seguente per generare il codice client Java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Segue un esempio che illustra l'utilizzo di autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Il comando seguente accetta il file della specifica ``servicefabric.json`` come input, genera il codice client Java nella cartella ``java-rest-api-     code`` e racchiude il codice nello spazio dei nomi ``servicefabricrest``. Dopo questo passaggio saranno disponibili le due cartelle ``models`` e ``implementation`` e i due file ``ServiceFabricClientAPIs.java`` e ``package-info.java`` generati nella cartella ``java-rest-api-code``.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Includere e usare il client generato nel progetto

1. Aggiungere il codice generato in modo appropriato nel progetto. È consigliabile creare una libreria usando il codice generato e includerla del progetto.
2. Se si crea una libreria, includere la dipendenza seguente nel progetto della libreria. Se si segue un approccio diverso, includere la dipendenza in modo appropriato.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Se ad esempio si usa il sistema di compilazione Maven, includere il codice seguente nel file ``pom.xml``:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Creare un elemento RestClient usando il codice seguente:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Usare l'oggetto client ed eseguire le chiamate appropriate in base alla necessità. Di seguito sono riportati alcuni esempi che illustrano l'utilizzo dell'oggetto client. Si presuppone che il pacchetto dell'applicazione venga compilato e caricato nell'archivio immagini prima di usare le API seguenti.
    * Effettuare il provisioning di un'applicazione
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Creare un'applicazione

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Informazioni sul codice generato
Per ogni API sono disponibili quattro overload di implementazione. Se sono presenti parametri facoltativi, si otterranno altre quattro varianti, inclusi i parametri facoltativi. Si consideri ad esempio l'API ``removeReplica``.
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Questa è la variante sincrona della chiamata API removeReplica
 2. **pubblica ServiceFuture\<Void > removeReplicaAsync (stringa nodeName, partitionId UUID, replicaId stringa, booleano forceRemove, timeout lungo, ServiceCallback finale\<Void > serviceCallback)**
    * Questa variante della chiamata API è utile se si vogliono usare la programmazione asincrona di base futura e i callback
 3. **Oggetto Observable pubblico\<Void > removeReplicaAsync (stringa nodeName, partitionId UUID, stringhe replicaId)**
    * Questa variante della chiamata API è utile se si vuole usare la programmazione asincrona reattiva
 4. **pubblica Observable\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (stringa nodeName, partitionId UUID, stringhe replicaId)**
    * Questa variante della chiamata API è utile se si vuole usare la programmazione asincrona reattiva e gestire la risposta REST non elaborata

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [API REST di Service Fabric](https://docs.microsoft.com/rest/api/servicefabric/)

