---
title: Creare un app Web Java in Linux - Servizio app di Azure
description: Creare, distribuire e ridimensionare app Web Java Spring Boot con il servizio app di Azure in Linux e Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 582ed374e7895d0b99f25ac033d0d4b1ec99104c
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171485"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Esercitazione: Creare un'app Web Java Spring Boot con il Servizio app di Azure in Linux e Azure Cosmos DB

Questa esercitazione illustra il processo di creazione, configurazione, distribuzione e ridimensionamento di app Web Java in Azure. Al termine, si avrà un'applicazione [Spring Boot](https://projects.spring.io/spring-boot/) che memorizza i dati in [Azure Cosmos DB](/azure/cosmos-db) in esecuzione nel [servizio app di Azure in Linux](/azure/app-service/containers).

![Applicazione Spring Boot che archivia i dati in Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database Cosmos DB.
> * Connettere un'app di esempio al database e testarla nell'ambiente locale
> * Distribuire l'app di esempio in Azure
> * Eseguire lo streaming dei log di diagnostica dal servizio app
> * Aggiungere altre istanze per ridimensionare l'app di esempio

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview) installata nel computer. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Clonare l'app TODO di esempio e preparare il repository

Questa esercitazione usa un'app di elenco TODO di esempio con un'interfaccia utente Web che chiama un'API REST Spring supportata da [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Il codice per l'app è disponibile in [GitHub](https://github.com/Microsoft/spring-todo-app). Per altre informazioni sulla scrittura di app Java con Spring e Cosmos DB, vedere l'esercitazione [Come usare Spring Boot Starter con l'API SQL di Azure Cosmos DB](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) e la [Guida introduttiva di Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Eseguire i comandi seguenti nel terminale per clonare il repository di esempio e configurare l'ambiente dell'app di esempio.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Creare un'istanza di Azure Cosmos DB

Seguire questi passaggi per creare un database Azure Cosmos DB nella propria sottoscrizione. L'app di elenco TODO si connetterà a questo database e archivierà i propri dati durante l'esecuzione, rendendo persistente lo stato dell'applicazione indipendentemente dalla posizione in cui viene eseguita.

1. Accedere all'interfaccia della riga di comando di Azure e impostare eventualmente la sottoscrizione se sono presenti più sottoscrizioni collegate alle proprie credenziali di accesso.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Creare un gruppo di risorse di Azure prendendo nota del nome.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Creare un'istanza di Azure Cosmos DB di tipo `GlobalDocumentDB`. Il nome dell'istanza di Cosmos DB deve contenere solo lettere minuscole. Prendere nota del campo `documentEndpoint` nella risposta del comando.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Ottenere la chiave di Azure Cosmos DB per la connessione all'app. Tenere a portata di mano `primaryMasterKey`, `documentEndpoint` perché saranno necessari nel passaggio successivo.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configurare le proprietà dell'app TODO

Aprire un terminale nel computer. Copia il file di script di esempio nel repository clonato per poterlo personalizzare per il database Cosmos DB appena creato.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Modificare `.scripts/set-env-variables.sh` in un editor qualsiasi e specificare le informazioni di connessione di Azure Cosmos DB. Per la configurazione del servizio app in Linux usare la stessa area di prima (`your-resource-group-region`) e il gruppo di risorse (`your-azure-group-name`) usato durante la creazione del database di Cosmos DB. Per WEBAPP_NAME scegliere un nome univoco, dato che non è possibile duplicare un nome di app Web in una distribuzione di Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Eseguire quindi lo script:

```bash
source .scripts/set-env-variables.sh
```
   
Queste variabili di ambiente vengono usate in `application.properties` nell'app di elenco TODO. I campi nel file delle proprietà definiscono una configurazione di repository predefinita per Spring Data:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

L'app di esempio usa quindi l'annotazione `@Document` importata da `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` per impostare un tipo di entità da archiviare e gestire con Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Usare Maven per eseguire l'esempio.

```bash
mvn package spring-boot:run
```

L'output sarà simile al seguente.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

È possibile accedere all'app TODO di Spring nell'ambiente locale usando questo collegamento dopo aver avviato l'app: [http://localhost:8080/](http://localhost:8080/).

 ![Accedere all'app TODO di Spring nell'ambiente locale](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Se vengono visualizzate eccezioni al posto del messaggio "Started TodoApplication", verificare che lo script `bash` nel passaggio precedente abbia esportato correttamente le variabili di ambiente e che i valori siano corretti per il database di Azure Cosmos DB creato.

## <a name="configure-azure-deployment"></a>Configurare la distribuzione di Azure

Aprire il file `pom.xml` nella directory `initial/spring-boot-todo` e aggiungere il seguente [plug-in Maven per la configurazione del servizio app di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md).

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Eseguire la distribuzione nel servizio app in Linux

Usare l'obiettivo Maven `azure-webapp:deploy` per distribuire l'app TODO nel servizio app di Azure in Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

L'output contiene l'URL per l'applicazione distribuita, in questo esempio `https://spring-todo-app.azurewebsites.net`. È possibile copiare l'URL nel Web browser o eseguire questo comando nella finestra del terminale per caricare l'app.

```bash
open https://spring-todo-app.azurewebsites.net
```

Verrà visualizzata l'app in esecuzione con l'URL remoto nella barra degli indirizzi:

 ![Applicazione Spring Boot in esecuzione con un URL remoto](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Aumentare le istanze dell'app TODO

Aumentare le istanze dell'applicazione aggiungendo un altro ruolo di lavoro:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie per un'altra esercitazione (vedere [Passaggi successivi](#next)), è possibile eliminarle eseguendo questo comando in Cloud Shell: 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

[Azure per sviluppatori Java](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), [Spring Data per Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) e [Servizio app in Linux](app-service-linux-intro.md).

Altre informazioni sull'esecuzione di app Java nel Servizio app in Linux sono disponibili nella Guida per gli sviluppatori.

> [!div class="nextstepaction"] 
> [Guida dello sviluppatore Java per il servizio app in Linux](configure-language-java.md)
