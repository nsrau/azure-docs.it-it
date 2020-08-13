---
title: "Esercitazione: Creare un'app Web Java con Azure Cosmos DB e l'API SQL"
description: "Esercitazione: Questa esercitazione sull'applicazione Web Java spiega come usare Azure Cosmos DB e l'API di SQL per archiviare e accedere ai dati da un'applicazione Java ospitata nei siti Web di Azure."
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9daa09209818b9e01738630911a1bb926e3c257d
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079944"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Esercitazione: Creare un'applicazione Web Java con Azure Cosmos DB e l'API di SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Questa esercitazione sull'applicazione Web Java illustra come usare il servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) per archiviare i dati e accedervi da un'applicazione Java ospitata in app Web del servizio app di Azure. Contenuto dell'articolo:

* Come creare un'applicazione JavaServer Pages (JSP) di base in Eclipse.
* Come usare il servizio Azure Cosmos DB tramite [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java).

Questa esercitazione illustra come creare un'applicazione di gestione delle attività basata su Web che consente di creare, recuperare e contrassegnare le attività come completate, come illustrato nella figura seguente. Tutte le attività nell'elenco attività vengono archiviate come documenti JSON in Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Applicazione Java My ToDo List":::

> [!TIP]
> Questa esercitazione sullo sviluppo dell’applicazione presuppone che l'utente abbia già acquisito familiarità con l'uso di Java. Se non si ha alcuna esperienza riguardo a Java o agli [strumenti richiesti come prerequisiti](#Prerequisites), è consigliabile scaricare il progetto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) completo da GitHub e creare l'applicazione usando le [istruzioni alla fine di questo articolo](#GetProject). Una volta creata la soluzione, è possibile leggere l'articolo per approfondire il codice nel contesto del progetto.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Prerequisiti per questa esercitazione sull'applicazione Web Java

Prima di iniziare questa esercitazione sullo sviluppo dell’applicazione, è necessario disporre di quanto segue:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable).
* [Eclipse IDE per sviluppatori Java EE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Un sito Web di Azure con Java Runtime Environment (ad esempio Tomcat o Jetty) abilitato.](../app-service/quickstart-java.md)

Se questi strumenti vengono installati per la prima volta, coreservlets.com fornisce una procedura dettagliata del processo di installazione nella sezione di avvio rapido dell'articolo relativo all'[esercitazione sull'installazione di TomCat7 e il relativo uso con Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Creare un account di Azure Cosmos DB

Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile proseguire con il [Passaggio 2: Creare l'applicazione Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Creare l'applicazione Java JSP

Per creare l'applicazione JSP:

1. Si inizierà con la creazione di un progetto Java. Avviare Eclipse, quindi fare clic su **File**, **New** (Nuovo) e quindi su **Dynamic Web Project** (Progetto Web dinamico). Se **Dynamic Web Project** (Progetto Web dinamico) non è presente nell'elenco dei progetti disponibili, seguire questa procedura: fare clic su **File**, su **New** (Nuovo) e su **Project** (Progetto), espandere **Web**, fare clic su **Dynamic Web Project** (Progetto Web dinamico), quindi selezionare **Next** (Avanti).
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="Sviluppo di applicazioni Java JSP":::

1. Immettere un nome di progetto nella casella **Project name** (Nome progetto) e dal menu a discesa **Target Runtime** (Runtime di destinazione) selezionare facoltativamente un valore, ad esempio Apache Tomcat v7.0, e quindi fare clic su **Finish** (Fine). Se si seleziona un runtime di destinazione, sarà possibile eseguire il progetto in locale tramite Eclipse.

1. Nella vista Project Explorer di Eclipse espandere il progetto. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.

1. Nella finestra di dialogo **New JSP File** (Nuovo file JSP) assegnare al file il nome **index.jsp**. Mantenere il nome **WebContent** per la cartella padre, come illustrato di seguito, e quindi fare clic su **Next** (Avanti).
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Esercitazione sull'applicazione Web Java - Creare un nuovo file JSP":::

1. Per le finalità di questa esercitazione, nella finestra di dialogo **Select JSP Template** (Seleziona modello JSP) selezionare **New JSP File (html)** (Nuovo file JSP - html), quindi fare clic su **Finish** (Fine).

1. Quando il file *index.jsp* viene aperto in Eclipse, aggiungere il testo per visualizzare **Hello World!** all'interno dell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato dovrebbe avere un aspetto analogo al codice seguente:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Salvare il file *index.jsp*.

1. Se nel passaggio 2 è stato impostato un runtime di destinazione, è possibile fare clic su **Project** (Progetto), quindi su **Run** (Esegui) per eseguire l'applicazione JSP in locale:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Esercitazione sull'applicazione Java - Hello World":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Installare Java SDK SQL

Il modo più semplice per inserire Java SDK SQL e le relative dipendenze è tramite [Apache Maven](https://maven.apache.org/). A tale scopo, sarà necessario convertire il progetto in un progetto Maven completando i passaggi seguenti:

1. Fare clic con il pulsante destro del mouse sul progetto in Project Explorer (Esplora progetti), scegliere **Configure** (Configura) e quindi fare clic su **Convert to Maven Project** (Converti in progetto Maven).

1. Nella finestra **Create new POM** (Crea nuovo POM) accettare le impostazioni predefinite e fare clic su **Finish** (Fine).

1. In **Project Explorer**, aprire il file pom.xml.

1. Nel riquadro **Dependencies** (Dipendenze) della scheda **Dependencies** (Dipendenze), fare clic su **Add** (Aggiungi).

1. Nella finestra **Select Dependency** (Seleziona dipendenza) eseguire le operazioni seguenti:
   
   * Nella casella **ID gruppo** immettere `com.azure`.
   * Nella casella **ID artefatto** immettere `azure-cosmos`.
   * Nella casella **Versione** immettere `4.0.1-beta.1`.
  
   In alternativa, aggiungere l'XML della dipendenza per ID gruppo e ID artefatto direttamente nel file *pom.xml*:

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. Fare clic su **OK**. Maven installerà SQL Java SDK o salverà il file pom.xml.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Usare il servizio Azure Cosmos DB in un'applicazione Java

Aggiungere ora i modelli, le visualizzazioni e i controller all'applicazione Web.

### <a name="add-a-model"></a>Aggiunta di un modello

Per prima cosa, definire un modello all'interno di un nuovo file *TodoItem.java*. La classe `TodoItem` definisce lo schema di un elemento insieme ai metodi getter e setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Aggiungere le classi DAO (Data Access Object)

Creare un oggetto DAO (Data Access Object) per astrarre in modo persistente gli elementi ToDo in Azure Cosmos DB. Per salvare gli elementi ToDo in una raccolta, il client deve conoscere i database e le raccolte da rendere permanenti (in base al riferimento dei collegamenti automatici). È in genere preferibile memorizzare nella cache database e raccolte appena possibile, in modo da evitare episodi di round trip del database.

1. Per richiamare il servizio Azure Cosmos DB, è necessario creare un'istanza di un nuovo oggetto `cosmosClient`. È in genere preferibile riutilizzare l'oggetto `cosmosClient` invece di creare un nuovo client per ogni richiesta successiva. È possibile riutilizzare il client definendolo all'interno della classe `cosmosClientFactory`. Aggiornare i valori HOST e MASTER_KEY salvati nel [passaggio 1](#CreateDB). Sostituire la variabile HOST con il proprio l'URI e MASTER_KEY con la propria chiave primaria. Usare il codice seguente per creare la classe `CosmosClientFactory` nel file di *CosmosClientFactory.java*:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. Creare un nuovo file *TodoDao.java* e aggiungere la classe `TodoDao` per creare, aggiornare, leggere ed eliminare gli elementi ToDo:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. Creare un nuovo file *MockDao.java* e aggiungere la classe `MockDao`, che implementa la classe `TodoDao` per eseguire operazioni CRUD sugli elementi:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. Creare un nuovo file *DocDbDao.java* e aggiungere la classe `DocDbDao`. Questa classe definisce il codice per la persistenza di elementi TodoItem nel contenitore, recupera il database e la raccolta, se esistono, o ne crea di nuovi in caso contrario. Questo esempio usa [Gson](https://code.google.com/p/google-gson/) per serializzare e deserializzare gli oggetti POJO (Plain Old Java Object) di TodoItem in documenti JSON. Per salvare gli elementi ToDo in una raccolta, il client deve conoscere i database e le raccolte da rendere permanenti (in base al riferimento dei collegamenti automatici). Questa classe definisce anche la funzione helper per recuperare i documenti in base a un altro attributo, ad esempio "ID") invece che con il collegamento automatico. È possibile usare il metodo helper per recuperare un documento JSON TodoItem in base all'ID e quindi deserializzarlo in POJO.

   È inoltre possibile usare l'oggetto client `cosmosClient` per ottenere una raccolta o un elenco di elementi TodoItem tramite una query SQL. Infine, definire il metodo delete per eliminare un elemento TodoItem dall'elenco. Il codice seguente illustra il contenuto della classe `DocDbDao`:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. Successivamente, creare un nuovo file *TodoDaoFactory.java* e aggiungere la classe `TodoDaoFactory`, che crea un nuovo oggetto DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Aggiunta di un controller

Aggiungere il controller *TodoItemController* all'applicazione. In questo progetto viene usato [Project Lombok](https://projectlombok.org/) per generare il costruttore, getter, setter e un generatore. In alternativa, è possibile scrivere il codice manualmente o farlo generare all'IDE:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Creare un servlet

Creare quindi un servlet per instradare le richieste HTTP al controller. Creare il file *ApiServlet.java* e definire il codice seguente al suo interno:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Collegare il resto dell'app Java

Dopo aver completato questi passaggi, è necessario creare un'interfaccia utente intuitiva e collegarla all'oggetto DAO.

1. Sarà necessaria un'interfaccia utente Web da visualizzare all'utente. A tale scopo, riscrivere il file *index.jsp* creato in precedenza con il codice seguente:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Infine, scrivere codice JavaScript lato client per il collegamento dell'interfaccia utente Web con il servlet:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. è necessario testare l'applicazione. Eseguire l'applicazione in locale e aggiungere alcuni elementi Todo specificando i valori relativi al nome e alla categoria dell'elemento e selezionando **Add Task** (Aggiungi attività). Quando l'elemento viene visualizzato, è possibile aggiornarne lo stato attivando o disattivando la relativa casella di controllo e facendo clic su **Update Tasks** (Aggiorna attività).

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Distribuire l'applicazione Java in Siti Web di Azure

Con Siti Web di Azure la procedura di distribuzione di applicazioni Java è molto semplice e consiste nell'esportazione di un'applicazione come file con WAR e nel relativo caricamento tramite controllo del codice sorgente (ad esempio Git) o FTP.

1. Per esportare l'applicazione come file WAR, fare clic con il pulsante destro del mouse sul progetto in **Project Explorer** (Esplora progetti), fare clic su **Export** (Esporta) e quindi su **WAR File** (File WAR).

1. Nella finestra **WAR Export** eseguire le operazioni seguenti:
   
   * Nella casella Web project immettere azure-documentdb-java-sample.
   * Nella casella Destination scegliere una destinazione in cui salvare il file WAR.
   * Fare clic su **Fine**.

1. A questo punto è sufficiente caricare il file nella directory **webapps** di Siti Web di Azure. Per istruzioni sul caricamento del file, vedere [Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure](../app-service/web-sites-java-add-app.md). Dopo aver caricato il file WAR nella directory webapps, l'ambiente di runtime rileverà che il file è stato aggiunto e lo caricherà automaticamente.

1. Per visualizzare il prodotto finito, passare a `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` e iniziare ad aggiungere le attività.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Ottenere il progetto da GitHub

Tutti gli esempi in questa esercitazione sono inclusi nel progetto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) su GitHub. Per importare il progetto todo in Eclipse, assicurarsi di avere il software e le risorse elencate nella sezione [Prerequisiti](#Prerequisites) , quindi eseguire le operazioni seguenti:

1. Installare [Project Lombok](https://projectlombok.org/). Lombok viene usato per generare costruttori, getter e setter nel progetto. Dopo aver scaricato il file lombok.jar, fare doppio clic per installarlo o eseguire l'installazione dalla riga di comando.

1. Se Eclipse è aperto, chiuderlo e riavviarlo per caricare Lombok.

1. In Eclipse scegliere **Import** (Importa) dal menu **File**.

1. Nella finestra **Import** (Importa) fare clic su **Git**, su **Projects from Git** (Progetti da Git) e quindi su **Next** (Avanti).

1. Nella schermata **Select Repository Source** (Seleziona origine repository) fare clic su **Clone URI** (Clona URI).

1. Nella schermata **Source Git Repository** (Repository Git di origine), nella casella **URI**, immettere https://github.com/Azure-Samples/documentdb-java-todo-app.git e quindi fare clic su **Next** (Avanti).

1. Nella schermata **Branch Selection** (Selezione ramo) assicurarsi che sia selezionata l'opzione **master** e quindi fare clic su **Next** (Avanti).

1. Nella schermata **Local Destination** (Destinazione locale) fare clic su **Browse** (Sfoglia) per selezionare una cartella in cui sia possibile copiare il repository e quindi fare clic su **Next** (Avanti).

1. Nella schermata **Select a wizard to use for importing projects** (Selezionare una procedura guidata per l'importazione di progetti) assicurarsi che l'opzione **Import existing projects** (Importa progetti esistenti) sia selezionata e quindi fare clic su **Next** (Avanti).

1. Nella schermata **Import Projects** (Importa progetti) deselezionare il progetto **DocumentDB** e quindi fare clic su **Finish** (Fine). Il progetto DocumentDB contiene Azure Cosmos DB Java SDK, che verrà aggiunto invece come dipendenza.

1. In **Esplora progetti** passare ad azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java e sostituire i valori HOST e MASTER_KEY con i valori URI e PRIMARY KEY dell'account Azure Cosmos DB, quindi salvare il file. Per altre informazioni, vedere [Passaggio 1. Creare un account di database Azure Cosmos](#CreateDB).

1. In **Project Explorer** (Esplora progetti) fare clic con il pulsante destro del mouse su **azure-documentdb-java-sample**, scegliere **Build Path** (Percorso compilazione) e quindi fare clic su **Configure Build Path** (Configura percorso compilazione).

1. Nella schermata **Java Build Path** (Percorso compilazione Java), nel riquadro a destra selezionare la scheda **Libraries** (Librerie) e quindi fare clic su **Add External JARs** (Aggiungi JAR esterni). Passare al percorso del file lombok.jar e fare clic su **Open** (Apri) e quindi su **OK**.

1. Vedere il passaggio 12 per aprire nuovamente la finestra **Properties** (Proprietà) e quindi nel riquadro a sinistra fare clic su **Targeted Runtimes** (Runtime di destinazione).

1. Nella schermata **Targeted Runtimes** (Runtime di destinazione) fare clic su **New** (Nuovo), selezionare **Apache Tomcat v7.0** e quindi fare clic su **OK**.

1. Vedere il passaggio 12 per aprire nuovamente la finestra **Properties** (Proprietà) e quindi nel riquadro a sinistra fare clic su **Project Facets** (Facet di progetto).

1. Nella schermata **Project Facets** (Facet di progetto) selezionare **Dynamic Web Module** (Modulo Web dinamico) e **Java** e quindi fare clic su **OK**.

1. Nella scheda **Servers** (Server) nella parte inferiore della schermata fare clic con il pulsante destro del mouse su **Tomcat v7.0 Server at localhost** (Server Tomcat v7.0 in localhost) e quindi fare clic su **Add and Remove** (Aggiungi e rimuovi).

1. Nella finestra **Add and Remove** (Aggiungi e rimuovi) spostare **azure-documentdb-java-sample** nella casella **Configured** (Configurato) e quindi fare clic su **Finish** (Fine).

1. Nella scheda **Server** fare clic con il pulsante destro del mouse su **Tomcat v7.0 Server at localhost** (Server Tomcat v7.0 in localhost) e quindi fare clic su **Restart** (Riavvia).

1. In un browser passare a `http://localhost:8080/azure-documentdb-java-sample/` e iniziare ad aggiungere voci all'elenco attività. Si noti che se sono stati modificati i valori di porta predefiniti, è necessario modificare la porta 8080 con il valore selezionato.

1. Per distribuire il progetto in un sito web di Azure, vedere il [passaggio 6. Distribuire l'applicazione in Siti Web di Azure](#Deploy).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'applicazione Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
