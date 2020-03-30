---
title: Eseguire la migrazione dei dati all'account DELL'API SQL del database Cosmos di Azure usando StriimMigrate data to Azure Cosmos DB SQL API account using Striim
description: Informazioni su come usare Striim per eseguire la migrazione dei dati da un database Oracle a un account API SQL di Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003275"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Eseguire la migrazione dei dati all'account DELL'API SQL del database Cosmos di Azure usando StriimMigrate data to Azure Cosmos DB SQL API account using Striim
 
The Striim image in the Azure marketplace offers continuous real-time data movement from data warehouses and databases to Azure. Durante lo spostamento dei dati, è possibile eseguire la denormalizzazione in linea, la trasformazione dei dati, abilitare l'analisi in tempo reale e scenari di creazione di report sui dati. È facile iniziare a usare Striim per spostare continuamente i dati aziendali nell'API SQL di Azure Cosmos DB. Azure offre un'offerta marketplace che semplifica la distribuzione di Striim e la migrazione dei dati in Azure Cosmos DB. 

Questo articolo illustra come usare Striim per eseguire la migrazione dei dati da un **database Oracle** a un account API SQL del **database Cosmos di Azure.**

## <a name="prerequisites"></a>Prerequisiti

* Se non si dispone di una sottoscrizione di [Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

* Un database Oracle in esecuzione in locale con alcuni dati in esso.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuire la soluzione Striim marketplace

1. Accedere al portale di [Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** e cercare Striim nel marketplace di Azure.Select Create a resource and search for **Striim** in the Azure marketplace. Selezionare la prima opzione e **Crea**.

   ![Trovare l'articolo del marketplace di Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Immettere quindi le proprietà di configurazione dell'istanza Striim. L'ambiente Striim viene distribuito in una macchina virtuale. Nel riquadro **Nozioni di base** immettere il nome utente della **macchina virtuale,** la **password della macchina virtuale** (questa password viene usata per SSH nella macchina virtuale). Selezionare **sottoscrizione**, **Gruppo di risorse**e Dettagli **percorso** in cui si vuole distribuire Striim. Al termine, selezionare **OK**.

   ![Configurare le impostazioni di base per Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. Nel riquadro **Impostazioni cluster Striim** scegliere il tipo di distribuzione Striim e le dimensioni della macchina virtuale.

   |Impostazione | valore | Descrizione |
   | ---| ---| ---|
   |Tipo di distribuzione Striim |Autonoma | Striim può essere eseguito in un tipo di distribuzione **autonomo** o **cluster.** La modalità autonoma distribuirà il server Striim in una singola macchina virtuale ed è possibile selezionare le dimensioni delle macchine virtuali a seconda del volume di dati. La modalità cluster distribuirà il server Striim in due o più macchine virtuali con le dimensioni selezionate. Gli ambienti cluster con più di 2 nodi offrono disponibilità elevata e failover automatici.</br></br> In questa esercitazione è possibile selezionare l'opzione Autonomo. Usare la macchina virtuale di dimensioni "Standard_F4s" predefinita.  | 
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

## <a name="configure-the-target-database"></a>Configurare il database di destinazione

In questa sezione verrà configurato l'account API SQL del database di Azure Cosmos come destinazione per lo spostamento dei dati.

1. Creare un [account API SQL del database di Azure Cosmos](create-cosmosdb-resources-portal.md) usando il portale di Azure.Create an Azure Cosmos DB SQL API account using the Azure portal.

1. Passare al riquadro Esplora dati nell'account Azure Cosmos.Navigate to the **Data Explorer** pane in your Azure Cosmos account. Selezionare **Nuovo contenitore** per creare un nuovo contenitore. Si supponga che si esegue la migrazione di *prodotti* e *ordina* dati dal database Oracle a Azure Cosmos DB. Creare un nuovo database denominato **StriimDemo** con un contenitore denominato **Orders**. Effettuare il provisioning del contenitore con **1000 RU** (in questo esempio vengono utilizzate 1000 RU, ma è consigliabile usare la velocità effettiva stimata per il carico di lavoro) e **/ORDER_ID** come chiave di partizione. Questi valori variano a seconda dei dati di origine. 

   ![Creare un account API SQL](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

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

1. Esistono diversi modi per creare applicazioni Striim. Selezionare **Inizia con modello** per iniziare con un modello esistente.

   ![Avviare l'app con il modello](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. Nel campo **Modelli di ricerca** digitare "Cosmos" e selezionare **Destinazione: Azure Cosmos DB** e quindi selezionare Oracle **CDC to Azure Cosmos DB**.

   ![Selezionare Oracle CDC to Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Nella pagina successiva assegnare un nome all'applicazione. È possibile fornire un nome, ad esempio **oraToCosmosDB** e quindi selezionare **Salva**.

1. Immettere quindi la configurazione di origine dell'istanza Oracle di origine. Immettere un valore per **Nome origine**. Il nome di origine è solo una convenzione di denominazione per l'applicazione Striim, è possibile utilizzare qualcosa come **src_onPremOracle**. Immettere i valori per gli altri parametri di origine **URL**, **Nome utente**, **Password**, scegliere **LogMiner** come lettore per leggere i dati da Oracle. Selezionare **Avanti** per continuare.

   ![Configurare i parametri di origine](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim controllerà l'ambiente e si assicurerà che possa connettersi all'istanza Oracle di origine, disponga dei privilegi corretti e che CDC sia stato configurato correttamente. Una volta convalidati tutti i valori, selezionare **Avanti**.

   ![Convalidare i parametri di origineValidate source parameters](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Selezionare le tabelle dal database Oracle di cui si desidera eseguire la migrazione. Ad esempio, scegliamo la tabella Orders e **selezioniamo Avanti**. 

   ![Selezionare le tabelle di origine](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Dopo aver selezionato la tabella di origine, è possibile eseguire operazioni più complesse, ad esempio il mapping e il filtro. In questo caso, si creerà semplicemente una replica della tabella di origine in Azure Cosmos DB. Quindi, selezionare **Avanti** per configurare la destinazione

1. Ora, configuriamo la destinazione:

   * **Nome destinazione:** specificare un nome descrittivo per la destinazione. 
   * **Input da** - Nell'elenco a discesa selezionare il flusso di input da quello creato nella configurazione Oracle di origine. 
   * **Raccolte:** immettere le proprietà di configurazione di Azure Cosmos DB di destinazione. La sintassi degli insiemi è **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. In questo esempio, il valore sarebbe "SYSTEM. ORDERS, StriimDemo.Orders". 
   * **AccessKey** - La chiave primaria dell'account Azure Cosmos.
   * **ServiceEndpoint:** URI dell'account Cosmos di Azure, disponibile nella sezione Chiavi del portale di Azure.ServiceEndpoint : The URI of your Azure Cosmos account, they can be found on the **Keys** section of the Azure portal. 

   Selezionare **Salva** e **Avanti**.

   ![Configurare i parametri di destinazione](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Successivamente, si arriverà a Progettazione flussi, in cui è possibile trascinare e rilasciare i connettori casella per creare le applicazioni di streaming. Non si apporterà alcuna modifica al flusso a questo punto. quindi andare avanti e distribuire l'applicazione selezionando il **Distribuisci App** pulsante.
 
   ![Distribuire l'app](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. Nella finestra di distribuzione è possibile specificare se si desidera eseguire determinate parti dell'applicazione in parti specifiche della topologia di distribuzione. Dal momento che è in esecuzione in una topologia di distribuzione semplice tramite Azure, useremo l'opzione predefinita.

   ![Utilizzare l'opzione predefinita](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Dopo la distribuzione, è possibile visualizzare in anteprima il flusso per vedere i dati che scorrono attraverso. Selezionare l'icona **dell'onda** e il bulbo oculare accanto ad essa. Selezionare il pulsante **Distribuito** nella barra dei menu superiore e selezionare **Avvia app**.

   ![Avviare l'app](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Utilizzando un lettore **CDC (Change Data Capture),** Striim rileva solo le nuove modifiche nel database. Se si dispone di dati che scorrono attraverso le tabelle di origine, verrà visualizzato. Tuttavia, poiché si tratta di una tabella demo, l'origine non è connessa ad alcuna applicazione. Se si utilizza un generatore di dati di esempio, è possibile inserire una catena di eventi nel database Oracle.If you use a sample data generator, you can insert a chain of events into your Oracle database.

1. Vedrai i dati che scorrono attraverso la piattaforma Striim. Striim raccoglie anche tutti i metadati associati alla tabella, il che è utile per monitorare i dati e assicurarsi che i dati applichino la destinazione giusta.

   ![Configurare la pipeline CDC](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Infine, è possibile accedere ad Azure e passare all'account Azure Cosmos.Finally, let's sign into Azure and navigate to your Azure Cosmos account. Aggiornare Esplora dati per vedere che i dati sono arrivati.  

   ![Convalidare i dati migrati in AzureValidate migrated data in Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Usando la soluzione Striim in Azure, è possibile eseguire continuamente la migrazione dei dati in Azure Cosmos DB da varie origini, ad esempio Oracle, Cassandra, MongoDB e vari altri a Azure Cosmos DB. Per saperne di più visita il [sito web di Striim,](https://www.striim.com/) [scarica una prova gratuita di 30 giorni di Striim](https://go2.striim.com/download-free-trial)e per eventuali problemi durante la configurazione del percorso di migrazione con Striim, invia una richiesta di [supporto gratuita.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Passaggi successivi

* Se si esegue la migrazione dei dati all'API SQL del database di Azure Cosmos, vedere come eseguire la migrazione dei [dati all'account dell'API Cassandra usando StriimIf](cosmosdb-cassandra-api-migrate-data-striim.md) you are migrating data to Azure Cosmos DB SQL API, see how to migrate data to Cassandra API account using Striim

* [Monitorare ed eseguire il debug dei dati con le metriche di Azure Cosmos DBMonitor and debug your data with Azure Cosmos DB metrics](use-metrics.md)
