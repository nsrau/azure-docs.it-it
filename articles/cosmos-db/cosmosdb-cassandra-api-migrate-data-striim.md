---
title: Eseguire la migrazione dei dati all'account API Cassandra DB di Azure usando StriimMigrate data to Azure Cosmos DB Cassandra API account using Striim
description: Informazioni su come usare Striim per eseguire la migrazione dei dati da un database Oracle a un account API Cassandra di Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 50028e81c4ca130aa3266c164a431dc935a271cb
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730046"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Eseguire la migrazione dei dati all'account API Cassandra DB di Azure usando StriimMigrate data to Azure Cosmos DB Cassandra API account using Striim

The Striim image in the Azure marketplace offers continuous real-time data movement from data warehouses and databases to Azure. Durante lo spostamento dei dati, è possibile eseguire la denormalizzazione in linea, la trasformazione dei dati, abilitare l'analisi in tempo reale e scenari di creazione di report sui dati. È facile iniziare a usare Striim per spostare continuamente i dati aziendali nell'API Cassandra di Azure Cosmos DB. Azure offre un'offerta marketplace che semplifica la distribuzione di Striim e la migrazione dei dati in Azure Cosmos DB. 

Questo articolo illustra come usare Striim per eseguire la migrazione dei dati da un **database Oracle** a un account **API Cassandra db di Azure.**

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

* Un database Oracle in esecuzione in locale con alcuni dati in esso.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuire la soluzione Striim marketplace

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** e cercare Striim nel marketplace di Azure.Select Create a resource and search for **Striim** in the Azure marketplace. Selezionare la prima opzione e **Crea**.

   ![Trovare l'articolo del marketplace di Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Immettere quindi le proprietà di configurazione dell'istanza Striim. L'ambiente Striim viene distribuito in una macchina virtuale. Nel riquadro **Nozioni di base** immettere il nome utente della **macchina virtuale,** la **password della macchina virtuale** (questa password viene usata per SSH nella macchina virtuale). Selezionare **sottoscrizione**, **Gruppo di risorse**e Dettagli **percorso** in cui si vuole distribuire Striim. Al termine, selezionare **OK**.

   ![Configurare le impostazioni di base per Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. Nel riquadro **Impostazioni cluster Striim** scegliere il tipo di distribuzione Striim e le dimensioni della macchina virtuale.

   |Impostazione | valore | Descrizione |
   | ---| ---| ---|
   |Tipo di distribuzione Striim |Autonoma | Striim può essere eseguito in un tipo di distribuzione **autonomo** o **cluster.** La modalità autonoma distribuirà il server Striim in una singola macchina virtuale ed è possibile selezionare le dimensioni delle macchine virtuali a seconda del volume di dati. La modalità cluster distribuirà il server Striim in due o più macchine virtuali con le dimensioni selezionate. Gli ambienti cluster con più di 2 nodi offrono disponibilità elevata e failover automatici.</br></br> In questa esercitazione è possibile selezionare l'opzione Autonomo. Usare la macchina virtuale di dimensioni "Standard_F4s" predefinita. | 
   | Nome del cluster Striim|    <> Striim_cluster_Name|  Nome del cluster Striim.|
   | Password del cluster Striim|   > Striim_cluster_password <|  Password per il cluster.|

   Dopo aver compilato il modulo, selezionare **OK** per continuare.

1. Nel riquadro delle impostazioni di **accesso Striim,** configurare l'indirizzo **IP pubblico** (scegliere i valori predefiniti), Nome di dominio per **Striim**, **Password amministratore** che si desidera utilizzare per accedere all'interfaccia utente di Striim. Configurare una rete virtuale e una subnet (scegliere i valori predefiniti). Dopo aver compilato i dettagli, selezionare **OK** per continuare.

   ![Impostazioni di accesso Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure convaliderà la distribuzione e si assicurerà che tutto sia corretto. la convalida richiede alcuni minuti. Al termine della convalida, selezionare **OK**.
  
1. Infine, esaminare le condizioni per l'utilizzo e selezionare **Crea** per creare l'istanza Striim. 

## <a name="configure-the-source-database"></a>Configurare il database di origine

In questa sezione viene configurato il database Oracle come origine per lo spostamento dei dati.  Per connetterti a Oracle è necessario il [driver Oracle JDBC.](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) Per leggere le modifiche dal database Oracle di origine, è possibile utilizzare il [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) o [le API XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Il driver Oracle JDBC deve essere presente nel percorso di classe Java di Striim per leggere, scrivere o rendere persistenti i dati dal database Oracle.

Scaricare il driver [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) nel computer locale. Verrà installato nel cluster Striim in un secondo momento.

## <a name="configure-target-database"></a>Configurare il database di destinazione

In questa sezione verrà configurato l'account API Cassandra di Azure Cosmos DB come destinazione per lo spostamento dei dati.

1. Creare un account API Cassandra DB di Azure usando il portale di Azure.Create an [Azure Cosmos DB Cassandra API account](create-cassandra-dotnet.md#create-a-database-account) using the Azure portal.

1. Passare al riquadro Esplora dati nell'account Azure Cosmos.Navigate to the **Data Explorer** pane in your Azure Cosmos account. Selezionare **Nuova tabella** per creare un nuovo contenitore. Si supponga che si esegue la migrazione di *prodotti* e *ordina* dati dal database Oracle a Azure Cosmos DB. Creare un nuovo Keyspace denominato StriimDemo con un contenitore Orders.Create a new Keyspace named **StriimDemo** with an Orders container. Effettuare il provisioning del contenitore con **1000 RU**(in questo esempio vengono utilizzate 1000 RU, ma è consigliabile usare la velocità effettiva stimata per il carico di lavoro) e **/ORDER_ID** come chiave primaria. Questi valori variano a seconda dei dati di origine. 

   ![Creare un account API Cassandra](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configurare Oracle per il flusso di dati di Azure Cosmos DBConfigure Oracle to Azure Cosmos DB data flow

1. Ora torniamo a Striim. Prima di interagire con Striim, installare il driver Oracle JDBC scaricato in precedenza.

1. Passare all'istanza di Striim distribuita nel portale di Azure.Navigate to the Striim instance that you deployed in the Azure portal. Selezionare il pulsante **Connetti** nella barra dei menu superiore e nella scheda **SSH** copiare l'URL nel campo **Login using VM local account.**

   ![Ottenere l'URL SSHGet the SSH URL](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Aprire una nuova finestra del terminale ed eseguire il comando SSH copiato dal portale di Azure.Open a new terminal window and run the SSH command you copied from the Azure portal. Questo articolo utilizza terminale in un MacOS, è possibile seguire le istruzioni simili utilizzando PuTTY o un client SSH diverso su un computer Windows. Quando richiesto, digitare **Yes** per continuare e immettere la **password** impostata per la macchina virtuale nel passaggio precedente.

   ![Connettersi a Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. A questo punto, aprire una nuova scheda del terminale per copiare il file **ojdbc8.jar** scaricato in precedenza. Usare il comando SCP seguente per copiare il file jar dal computer locale alla cartella tmp dell'istanza Striim in esecuzione in Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copiare il file Jar dalla macchina della posizione a Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Successivamente, tornare alla finestra in cui è stato eseguito SSH per l'istanza Striim e Login come sudo. Spostare il file **ojdbc8.jar** dalla directory **/tmp** nella directory **lib** dell'istanza Striim con i seguenti comandi:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Spostare il file Jar nella cartella lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Dalla stessa finestra del terminale, riavviare il server Striim eseguendo i seguenti comandi:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim ci vorrà un minuto per l'avvio. Se si desidera visualizzare lo stato, eseguire il comando seguente: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Tornare ad Azure e copiare l'indirizzo IP pubblico della macchina virtuale Striim.Now, navigate back to Azure and copy the Public IP address of your Striim VM. 

   ![Copia indirizzo IP macchina virtuale Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Per accedere all'interfaccia utente Web di Striim, aprire una nuova scheda in un browser e copiare l'indirizzo IP pubblico seguito da: 9080. Accedere usando il nome utente **dell'amministratore,** insieme alla password di amministratore specificata nel portale di Azure.Sign in by using the admin username, along with the admin password you specified in the Azure portal.

   ![Accedi a Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Ora arriverai alla home page di Striim. Sono disponibili tre riquadri diversi: **Dashboard,** **App**e **SourcePreview**. Il riquadro Dashboard consente di spostare i dati in tempo reale e visualizzarli. Il riquadro App contiene le pipeline di dati di streaming o i flussi di dati. Sulla destra della pagina è SourcePreview dove è possibile visualizzare in anteprima i dati prima di spostarli.

1. Seleziona il riquadro **App,** per il momento ci concentreremo su questo riquadro. Ci sono una varietà di applicazioni di esempio che è possibile utilizzare per conoscere Striim, tuttavia in questo articolo si creerà il nostro. Seleziona il pulsante **Aggiungi app** nell'angolo in alto a destra.

   ![Aggiungere l'app Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Esistono diversi modi per creare applicazioni Striim. Selezionare **Inizia da Scratch** per questo scenario.

   ![Avviare l'app da zero](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Assegnare un nome descrittivo per l'applicazione, ad esempio **oraToCosmosDB** e selezionare **Salva**.

   ![Creare una nuova applicazione](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Si arriverà a Progettazione flussi, in cui è possibile trascinare e rilasciare i connettori box per creare le applicazioni di streaming. Digita **Oracle** nella barra di ricerca, trascina e rilascia l'origine **Oracle CDC** nell'area di disegno dell'app.  

   ![Sorgente Oracle CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Immettere le proprietà di configurazione di origine dell'istanza Oracle. Il nome di origine è solo una convenzione di denominazione per l'applicazione Striim , è possibile utilizzare un nome, ad esempio **src_onPremOracle**. Immettere anche altri dettagli come tipo di scheda, URL di connessione, nome utente, password, nome della tabella. Selezionare **Salva** per continuare.

   ![Configurare i parametri di origine](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. A questo punto, fare clic sull'icona onda del flusso per connettere l'istanza di Azure Cosmos DB di destinazione. 

   ![Connessione a destinazione](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Prima di configurare la destinazione, assicurarsi di aver aggiunto un certificato radice di [Baltimora all'ambiente Java di Striim.](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)

1. Immettere le proprietà di configurazione dell'istanza del database Cosmos di Azure di destinazione e selezionare **Salva** per continuare. Ecco i parametri chiave da notare:

   * **Adattatore** - Utilizzare **DatabaseWriter**. Quando si scrive nell'API Cassandra di Azure Cosmos, è necessario DatabaseWriter.When writing to Azure Cosmos DB Cassandra API, DatabaseWriter is required. Il driver Cassandra 3.6.0 è in bundle con Striim. Se il DatabaseWriter supera il numero di RU di cui è stato eseguito il provisioning nel contenitore Azure Cosmos, l'applicazione si arresterà in modo anomalo.

   * **URL di connessione:** specificare l'URL di connessione JDBC del database di Azure Cosmos.Connection URL - Specify your Azure Cosmos DB JDBC connection URL. L'URL è nel formato`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Nome utente:** specificare il nome dell'account Azure Cosmos.Username - Specify your Azure Cosmos account name.
   
   * **Password:** specificare la chiave primaria dell'account Azure Cosmos.

   * **Tabelle:** le tabelle di destinazione devono avere chiavi primarie e le chiavi primarie non possono essere aggiornate.

   ![Configurare le proprietà di destinazione](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Configurare le proprietà di destinazione](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Ora, andremo avanti ed eseguire l'applicazione Striim. Nella barra dei menu superiore selezionare **Creato**, quindi **Distribuisci app**. Nella finestra di distribuzione è possibile specificare se si desidera eseguire determinate parti dell'applicazione in parti specifiche della topologia di distribuzione. Dal momento che è in esecuzione in una topologia di distribuzione semplice tramite Azure, useremo l'opzione predefinita.

   ![Distribuire l'app](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Ora, andremo avanti e visualizzare in anteprima il flusso per vedere i dati che scorrono attraverso il Striim. Fare clic sull'icona dell'onda e fare clic sull'icona a forma di occhio accanto ad essa. Dopo la distribuzione, è possibile visualizzare in anteprima il flusso per vedere i dati che scorrono attraverso. Selezionare l'icona **dell'onda** e il **bulbo oculare** accanto ad essa. Selezionare il pulsante **Distribuito** nella barra dei menu superiore e selezionare **Avvia app**.

   ![Avviare l'app](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Utilizzando un lettore **CDC (Change Data Capture),** Striim rileva solo le nuove modifiche nel database. Se si dispone di dati che scorrono attraverso le tabelle di origine, verrà visualizzato. Tuttavia, poiché si tratta di una tabella di esempio, l'origine che non è connessa ad alcuna applicazione. Se si utilizza un generatore di dati di esempio, è possibile inserire una catena di eventi nel database Oracle.If you use a sample data generator, you can insert a chain of events into your Oracle database.

1. Vedrai i dati che scorrono attraverso la piattaforma Striim. Striim raccoglie anche tutti i metadati associati alla tabella, il che è utile per monitorare i dati e assicurarsi che i dati applichino la destinazione giusta.

   ![Impostare la pipeline CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Infine, è possibile accedere ad Azure e passare all'account Azure Cosmos.Finally, let's sign into Azure and navigate to your Azure Cosmos account. Aggiornare Esplora dati per vedere che i dati sono arrivati. 

Usando la soluzione Striim in Azure, è possibile eseguire continuamente la migrazione dei dati in Azure Cosmos DB da varie origini, ad esempio Oracle, Cassandra, MongoDB e vari altri a Azure Cosmos DB. Per saperne di più visita il [sito web di Striim,](https://www.striim.com/) [scarica una prova gratuita di 30 giorni di Striim](https://go2.striim.com/download-free-trial)e per eventuali problemi durante la configurazione del percorso di migrazione con Striim, invia una richiesta di [supporto gratuita.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Passaggi successivi

* Se si esegue la migrazione dei dati all'API SQL del database di Azure Cosmos, vedere come eseguire la migrazione dei [dati all'account dell'API Cassandra usando StriimIf](cosmosdb-sql-api-migrate-data-striim.md) you are migrating data to Azure Cosmos DB SQL API, see how to migrate data to Cassandra API account using Striim

* [Monitorare ed eseguire il debug dei dati con le metriche di Azure Cosmos DBMonitor and debug your data with Azure Cosmos DB metrics](use-metrics.md)
