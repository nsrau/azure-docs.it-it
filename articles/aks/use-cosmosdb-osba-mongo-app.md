---
title: Integrare l'applicazione MongoDB esistente con l'API Azure Cosmos DB per MongoDB e Open Service Broker for Azure (OSBA)
description: In questo articolo viene descritto come integrare un'applicazione Java e MongoDB esistente con l'API Azure Cosmos DB per MongoDB usando Open Service Broker for Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 04b513de1d47749bb87b7aaf79839389ab4d7290
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082618"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrare l'applicazione MongoDB esistente con l'API Azure Cosmos DB per MongoDB e Open Service Broker for Azure (OSBA)

Azure Cosmos DB è un servizio di database multimodello e distribuito a livello globale. Fornisce anche la compatibilità del protocollo di collegamento con diverse API NoSQL, anche per MongoDB. L'API Cosmos DB per MongoDB consente di usare Cosmos DB con l'applicazione MongoDB esistente senza dover modificare l'implementazione o i driver di database dell'applicazione. È anche possibile effettuare il provisioning di un servizio di Cosmos DB usando Open Service Broker for Azure.

In questo articolo un'applicazione Java esistente che usa un database MongoDB viene aggiornata per usare un database Cosmos DB con Open Service Broker for Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter procedere, è necessario:
    
* Avere creato un [cluster del servizio Azure Kubernetes](kubernetes-walkthrough.md).
* Avere [installato e configurato Open Service Broker for Azure nel cluster del servizio Azure Kubernetes](integrate-azure.md). 
* Avere installato e configurato l'[interfaccia della riga di comando del catalogo di servizi](https://svc-cat.io/docs/install/) per eseguire i comandi `svcat`.
* Avere un database [MongoDB](https://www.mongodb.com/) esistente. MongoDB, ad esempio, potrebbe essere in esecuzione nel [computer di sviluppo](https://docs.mongodb.com/manual/administration/install-community/) o in una [macchina virtuale di Azure](../virtual-machines/linux/install-mongodb.md).
* Avere la possibilità di connettersi ed eseguire query sul database MongoDB, ad esempio tramite la [shell mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione
    
In questo articolo si usa l'[applicazione di esempio spring music in Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) per illustrare un'applicazione che usa un database MongoDB.
    
Clonare l'applicazione da GitHub e passare alla relativa directory:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Preparare l'applicazione per usare il database MongoDB

L'applicazione di esempio spring music offre molte opzioni per le origini dati. In questo articolo la si configura per usare un database MongoDB esistente. 

Aggiungere il codice YAML seguente alla fine di *src/main/resources/application.yml*. Questa aggiunta crea un profilo denominato *mongodb* e configura un URI e un nome di database. Sostituire l'URI con le informazioni di connessione al database MongoDB esistente. L'aggiunta dell'URI, che contiene un nome utente e una password, direttamente a questo file è per l'**uso esclusivo a fini di sviluppo** e **non deve mai essere applicata al controllo della versione**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Quando si avvia l'applicazione e le si comunica di usare il profilo *mongodb*, si connette al database MongoDB e lo usa per archiviare i dati dell'applicazione.

Per compilare l'applicazione:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Avviare l'applicazione e comunicarle di usare il profilo *mongodb*:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Passare a http://localhost:8080 nel browser.

![App Spring Music con i dati predefiniti](media/music-app.png)

Si noti che l'applicazione è stata popolata con alcuni [dati predefiniti](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagire eliminando alcuni album esistenti e creandone alcuni nuovi.

Per verificare che l'applicazione stia usando il database MongoDB, connettersi ed eseguire una query sul database *musicdb*:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

L'esempio precedente usa la [shell mongo](https://docs.mongodb.com/manual/mongo/) per connettersi al database MongoDB ed eseguire la query. È anche possibile verificare che le modifiche siano persistenti arrestando, riavviando e tornando all'applicazione nel browser. Si noti che le modifiche apportate sono ancora presenti.


## <a name="create-a-cosmos-db-database"></a>Creare un database Cosmos DB

Per creare un database Cosmos DB in Azure con Open Service Broker, usare il comando `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Il comando precedente effettua il provisioning di un database Cosmos DB in Azure nel gruppo di risorse *MyResourceGroup* nell'area *eastus*. Altre informazioni sui parametri *resourceGroup*, *location* e altri parametri JSON specifici di Azure sono disponibili nella [documentazione di riferimento del modulo Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Per verificare il completamento del provisioning del database, usare il comando `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Il database è pronto quando viene visualizzato *Ready* sotto *STATUS*.

Dopo che il database ha completato il provisioning, è necessario associarne i metadati a un [segreto di Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Le altre applicazioni possono quindi accedere a tali dati dopo che sono stati associati a un segreto. Per associare i metadati del database a un segreto, usare il comando `svcat bind`:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Usare il database Cosmos DB con l'applicazione

Per usare il database Cosmos DB con l'applicazione, è necessario conoscere l'URI per la connessione. Per ottenere queste informazioni, usare il comando `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Il comando precedente ottiene il segreto *musicdb* e visualizza solo l'URI. I segreti sono archiviati in formato base64, quindi il comando precedente ne esegue anche la decodifica.

Con l'URI del database Cosmos DB, aggiornare *src/main/resources/application.yml* per usarlo:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

L'aggiornamento dell'URI, che contiene un nome utente e una password, direttamente in questo file è per l'**uso esclusivo a fini di sviluppo** e **non deve mai essere applicato al controllo della versione**.

Ricompilare e avviare l'applicazione per iniziare a usare il database Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Si noti che l'applicazione usa ancora il profilo *mongodb* e un URI che inizia con *mongodb://* per connettersi al database Cosmos DB. L'[API Azure Cosmos DB per MongoDB](../cosmos-db/mongodb-introduction.md) consente questa compatibilità. Consente all'applicazione di continuare a funzionare come se stesse usando un database MongoDB, ma in realtà sta usando Cosmos DB.

Passare a http://localhost:8080 nel browser. Si noti che sono stati ripristinati i dati predefiniti. Interagire eliminando alcuni album esistenti e creandone alcuni nuovi. È possibile verificare che le modifiche siano persistenti arrestando, riavviando e tornando all'applicazione nel browser. Si noti che le modifiche apportate sono ancora presenti. Le modifiche vengono rese persistenti nel database Cosmos DB creato usando Open Service Broker for Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Eseguire l'applicazione nel cluster del servizio Azure Kubernetes

È possibile usare [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) per distribuire l'applicazione nel cluster del servizio Azure Kubernetes. Azure Dev Spaces consente di generare elementi, ad esempio grafici Dockefiles e Helm, e di distribuire ed eseguire un'applicazione nel servizio Azure Kubernetes.

Per abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Usare gli strumenti di Azure Dev Spaces per preparare l'applicazione per l'esecuzione nel servizio Azure Kubernetes:

```cmd
azds prep --public
```

Questo comando genera diversi elementi, inclusa una cartella *charts/*, ovvero il grafico Helm, nella radice del progetto. Questo comando non può generare un documento *Dockerfile* per questo progetto specifico, quindi è necessario crearlo.

Nella radice del progetto creare un file denominato *Dockerfile* con questo contenuto:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

È anche necessario aggiornare la proprietà *configurations.develop.build* in *azds.yaml* impostandola su *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

È anche necessario aggiornare l'attributo *containerPort* impostandolo su *8080* in *charts/spring-music/templates/deployment.yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Per distribuire l'applicazione nel servizio Azure Kubernetes:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Passare all'URL visualizzato nei log. Nell'esempio precedente si userebbe *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Verificare che sia presente l'applicazione con le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come aggiornare un'applicazione esistente dall'uso di MongoDB all'uso dell'API Cosmos DB per MongoDB. Questo articolo ha anche illustrato come effettuare il provisioning di un servizio di Cosmos DB usando Open Service Broker for Azure e distribuendo tale applicazione nel servizio Azure Kubernetes con Azure Dev Spaces.

Per altre informazioni su Cosmos DB, Open Service Broker for Azure e Azure Dev Spaces, vedere:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Sviluppare con Dev Spaces](../dev-spaces/azure-dev-spaces.md)
