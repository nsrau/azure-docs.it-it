---
title: Implementare Azure Databricks con un endpoint di Cosmos DB
description: Questa esercitazione descrive come implementare Azure Databricks in una rete virtuale con un endpoint di servizio abilitato per Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: d1268ea2cfc22e6350edb32230588a497be8bc79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054608"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Esercitazione: Implementare Azure Databricks con un endpoint di Cosmos DB

Questa esercitazione descrive come implementare un ambiente Databricks con rete virtuale inserita con un endpoint di servizio abilitato per Cosmos DB.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks in una rete virtuale
> * Crea un endpoint di servizio di Cosmos DB
> * Creare un account Cosmos DB e importare i dati
> * Creare un cluster di Azure Databricks
> * Eseguire query su Cosmos DB da un notebook di Azure Databricks

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, eseguire le operazioni seguenti:

* Creare un'[area di lavoro di Azure Databricks in una rete virtuale](quickstart-create-databricks-workspace-vnet-injection.md).

* Scaricare il [connettore Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Scaricare dati di esempio del [NOAA National Center for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Selezionare uno stato o un'area, quindi selezionare **Cerca**. Nella pagina successiva, accettare le impostazioni predefinite e selezionare **Cerca**. Quindi selezionare **Scarica CSV** sul lato sinistro della pagina per scaricare i risultati.

* Scaricare il [binario precompilato](https://aka.ms/csdmtool) dell'Utilità di migrazione dati di Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Crea un endpoint di servizio di Cosmos DB

1. Dopo aver distribuito un'area di lavoro Azure Databricks in una rete virtuale, passare alla rete virtuale nel [portale di Azure](https://portal.azure.com). Osservare le subnet pubbliche e private che sono state create con la distribuzione di Databricks.

   ![Subnet della rete virtuale](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selezionare la *subnet pubblica* e creare un endpoint di servizio di Cosmos DB. Quindi, scegliere **Salva**.
   
   ![Aggiungere un endpoint di servizio di Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Creare un account Cosmos DB

1. Aprire il Portale di Azure. In alto a sinistra nella schermata selezionare **Crea una risorsa > Database > Azure Cosmos DB**.

2. Compilare **Dettagli dell'istanza** nella scheda **Generale** con le impostazioni seguenti:

   |Impostazione|Valore|
   |-------|-----|
   |Sottoscrizione|*sottoscrizione in uso*|
   |Gruppo di risorse|*Il gruppo di risorse*|
   |Nome account|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Località|Stati Uniti occidentali|
   |Ridondanza geografica|Disabilitazione|
   |Scritture in più aree|Abilita|

   ![Aggiungere un endpoint di servizio di Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selezionare la scheda **Rete** e configurare la rete virtuale. 

   a. Scegliere la rete virtuale creata come prerequisito e quindi selezionare *public-subnet*. Si osservi che per *private-subnet* è presente la nota *L'endpoint 'Microsoft AzureCosmosDB' è mancante*. Questo perché è stato abilitato solo l'endpoint di servizio di Cosmos DB nella *public-subnet*.

   b. Verificare di aver abilitato l'opzione **Consentire l'accesso dal portale di Azure**. Questa impostazione consente di accedere all'account Cosmos DB dal portale di Azure. Se questa opzione è impostata su **Nega**, verranno visualizzati errori quando si prova ad accedere all'account. 

   > [!NOTE]
   > Anche se non è necessario per questa esercitazione, è possibile abilitare *Consenti l'accesso dal mio indirizzo IP* se si vuole poter accedere all'account Cosmos DB dal computer locale. Ad esempio, se ci si connette a un account usando l'SDK di Cosmos DB, è necessario abilitare questa impostazione. Se è disabilitata, si riceveranno errori "Accesso negato".

   ![Impostazioni di rete dell'account Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selezionare **Rivedi e crea**, quindi **Crea** per creare un account Cosmos DB all'interno della rete virtuale.

5. Dopo aver creato l'account Cosmos DB, passare a **Chiavi** sotto **Impostazioni**. Copiare la stringa di connessione primaria e salvarla in un editor di testo per usarla in seguito.

    ![Pagina Chiavi dell'account Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selezionare **Esplora dati** e **Nuova raccolta** per aggiungere un nuovo database e una raccolta al proprio account Cosmos DB.

    ![Nuova raccolta di Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Caricare dati in Cosmos DB

1. Aprire la versione con interfaccia grafica dell'[utilità di migrazione dati per Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Utilità di migrazione dati di Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Nella scheda **Informazioni origine** selezionare **File CSV** nell'elenco a discesa **Importa da**. Quindi, selezionare **Aggiungi file** e aggiungi il file CSV dei dati Storm scaricato come prerequisito.

    ![Informazioni origine dell'Utilità di migrazione dati di Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Nella scheda **Informazioni destinazione** immettere la stringa di connessione. Il formato della stringa di connessione è `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint e AccountKey sono inclusi nella stringa di connessione primaria salvata nella sezione precedente. Aggiungere `Database=<your database name>` alla fine della stringa di connessione, quindi selezionare **Verifica**. Quindi, aggiungere il Nome raccolta e la chiave di partizione.

    ![Informazioni destinazione dell'Utilità di migrazione dati di Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selezionare **Avanti** fino a visualizzare la pagina Riepilogo. Quindi, selezionare **Importa**.

## <a name="create-a-cluster-and-add-library"></a>Creare un cluster e aggiungere una libreria

1. Passare al servizio Azure Databricks nel [portale di Azure](https://portal.azure.com) e selezionare **Avvia l'area di lavoro**.

   ![Avviare l'area di lavoro di Azure Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Creare un nuovo cluster. Scegliere un Nome cluster e accettare le impostazioni predefinite rimanenti.

   ![Nuove impostazioni del cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Dopo aver creato il cluster, passare alla pagina del cluster e selezionare la scheda **Librerie**. Selezionare **Installa nuovo** e caricare il file con estensione jar del connettore Spark per installare la libreria.

    ![Installare la libreria del connettore Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    È possibile verificare che la libreria sia stata installata nella scheda **Librerie**.

    ![Scheda Librerie del cluster Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Eseguire query su Cosmos DB da un notebook di Databricks

1. Passare all'area di lavoro Azure Databricks e creare un nuovo notebook Python.

    ![Creare un nuovo notebook Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Eseguire il codice Python seguente per impostare la configurazione della connessione di Cosmos DB. Modificare di conseguenza le voci **Endpoint**, **Masterkey**, **Database** e **Raccolta**.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Usare il seguente codice Python per caricare i dati e creare una vista temporanea.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Usare il comando magic seguente per eseguire un'istruzione SQL che restituisce dati.

    ```python
    %sql
    select * from storm
    ```

    L'area di lavoro di Databricks con rete virtuale inserita è stata correttamente connessa a una risorsa di Cosmos DB abilitata per l'endpoint di servizio. Per altre informazioni su come connettersi a Cosmos DB, vedere [Azure Cosmos DB Connector for Apache Spark](https://github.com/Azure/azure-cosmosdb-spark) (Connettore di Azure Cosmos DB per Apache Spark).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'area di lavoro di Azure Databricks e tutte le risorse correlate. L'eliminazione del processo consente di evitare la fatturazione non necessaria. Se si prevede di usare l'area di lavoro di Azure Databricks in futuro, è possibile arrestare il cluster e riavviarlo in un secondo momento. Se non si intende continuare a usare quest'area di lavoro di Azure Databricks, eliminare tutte le risorse create in questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome del gruppo di risorse creato.

2. Nella pagina del gruppo di risorse selezionare **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi selezionare di nuovo **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'area di lavoro di Azure Databricks in una rete virtuale ed è stato usato il connettore Spark per Cosmos DB per eseguire query sui dati di Cosmos DB da Databricks. Per altre informazioni sull'uso di Azure Databricks in una rete virtuale, passare all'esercitazione per l'uso di SQL Server con Azure Databricks.

> [!div class="nextstepaction"]
> [Esercitazione: Query a SQL Server Linux Docker container in a virtual network from an Azure Databricks notebook](vnet-injection-sql-server.md) (Eseguire query in un contenitore Docker Linux di SQL Server in una rete virtuale da un notebook di Azure Databricks)
