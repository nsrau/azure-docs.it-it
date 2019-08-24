---
title: Migrare i dati in Azure Cosmos DB account API SQL tramite StriIm
description: Informazioni su come usare StriIm per eseguire la migrazione dei dati da un database Oracle a un account Azure Cosmos DB API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 27961413d0dddc165f90ebde1c5e1aee6b8d9fd3
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981843"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrare i dati in Azure Cosmos DB account API SQL tramite StriIm
 
L'immagine di StriIm in Azure Marketplace offre lo spostamento continuo dei dati in tempo reale da data warehouse e database ad Azure. Durante lo trasferimento dei dati, è possibile eseguire la denormalizzazione in linea, la trasformazione dei dati, abilitare l'analisi in tempo reale e gli scenari di creazione di report di dati. È facile iniziare a usare StriIm per spostare continuamente i dati aziendali in Azure Cosmos DB API SQL. Azure offre un'offerta di Marketplace che semplifica la distribuzione di StriIm e la migrazione dei dati in Azure Cosmos DB. 

Questo articolo illustra come usare StriIm per eseguire la migrazione dei dati da un **database Oracle** a un **account API SQL Azure Cosmos DB**.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

* Un database Oracle in esecuzione in locale con alcuni dati.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuire la soluzione StriIm Marketplace

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** e cercare **StriIm** in Azure Marketplace. Selezionare la prima opzione e **creare**.

   ![Trova l'elemento StriIm Marketplace](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Immettere quindi le proprietà di configurazione dell'istanza di StriIm. L'ambiente StriIm viene distribuito in una macchina virtuale. Dal riquadro **nozioni di base** immettere il **nome utente della macchina**virtuale e la password della **VM** . questa password viene usata per SSH nella macchina virtuale. Selezionare la **sottoscrizione**, il **gruppo di risorse**e **i dettagli della località** in cui si vuole distribuire StriIm. Al termine, selezionare **OK**.

   ![Configurare le impostazioni di base per StriIm](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. Nel riquadro **Impostazioni cluster StriIm** scegliere il tipo di distribuzione di StriIm e le dimensioni della macchina virtuale.

   |Impostazione | Valore | Descrizione |
   | ---| ---| ---|
   |Tipo di distribuzione StriIm |Autonomi | StriIm può essere eseguito in un tipo di distribuzione autonomo o **cluster** . La modalità autonoma distribuirà il server StriIm in una singola macchina virtuale ed è possibile selezionare le dimensioni delle VM a seconda del volume di dati. La modalità cluster distribuirà il server StriIm in due o più macchine virtuali con le dimensioni selezionate. Gli ambienti cluster con più di 2 nodi offrono disponibilità elevata e failover automatici.</br></br> In questa esercitazione è possibile selezionare l'opzione autonoma. Usare la VM di dimensioni predefinite "Standard_F4s".  | 
   | Nome del cluster StriIm|    < Striim_cluster_Name >|  Nome del cluster StriIm.|
   | Password del cluster StriIm|   < Striim_cluster_password >|  Password per il cluster.|

   Dopo aver compilato il modulo, selezionare **OK** per continuare.

1. Nel riquadro **impostazioni di accesso StriIm** configurare l' **indirizzo IP pubblico** (scegliere i valori predefiniti), il **nome di dominio per StriIm**, la **password amministratore** che si vuole usare per accedere all'interfaccia utente di StriIm. Configurare una VNET e una subnet (scegliere i valori predefiniti). Dopo aver compilato i dettagli, fare clic su **OK** per continuare.

   ![Impostazioni di accesso StriIm](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure convaliderà la distribuzione e assicurerà che tutti gli elementi siano soddisfacenti. il completamento della convalida richiede alcuni minuti. Al termine della convalida, fare clic su **OK**.
  
1. Infine, rivedere le condizioni per l'utilizzo e selezionare **Crea** per creare l'istanza di StriIm. 

## <a name="configure-the-source-database"></a>Configurare il database di origine 

In questa sezione viene configurato il database Oracle come origine per lo spostamento dei dati.  Per la connessione a Oracle è necessario il [driver JDBC per Oracle](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Per leggere le modifiche dal database Oracle di origine, è possibile usare le API [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) o [xStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Il driver JDBC per Oracle deve essere presente nel CLASSPATH Java di StriIm per la lettura, la scrittura o la conservazione dei dati dal database Oracle.

Scaricare il driver [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) nel computer locale. Verrà installato nel cluster StriIm in un secondo momento.

## <a name="configure-the-target-database"></a>Configurare il database di destinazione

In questa sezione si configurerà l'account dell'API di Azure Cosmos DB SQL come destinazione per lo spostamento dei dati.

1. Creare un [account dell'API SQL Azure Cosmos DB](create-cosmosdb-resources-portal.md) usando il portale di Azure.

1. Passare al riquadro **Esplora dati** nell'account Azure Cosmos. Selezionare **nuovo contenitore** per creare un nuovo contenitore. Si supponga di migrare i *prodotti* e *ordinare* i dati dal database Oracle al Azure Cosmos DB. Creare un nuovo database denominato **StriimDemo** con un contenitore denominato **Orders**. Eseguire il provisioning del contenitore con **1000 ur** (questo esempio usa 1000 ur, ma è necessario usare la velocità effettiva stimata per il carico di lavoro) e **/order_id** come chiave di partizione. Questi valori variano a seconda dei dati di origine. 

   ![Creare un account API SQL](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configurare Oracle per Azure Cosmos DB flusso di dati

1. Ora, torniamo a StriIm. Prima di interagire con StriIm, installare il driver JDBC per Oracle scaricato in precedenza.

1. Passare all'istanza di StriIm distribuita nel portale di Azure. Selezionare il pulsante **Connetti** nella barra dei menu superiore e nella scheda **SSH** copiare l'URL in **login usando il campo account locale VM** .

   ![Ottenere l'URL SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Aprire una nuova finestra del terminale ed eseguire il comando SSH copiato dal portale di Azure. Questo articolo usa il terminale in MacOS. è possibile seguire le istruzioni simili usando PuTTy o un altro client SSH in un computer Windows. Quando richiesto, digitare **Yes (sì** ) per continuare e immettere la **password** impostata per la macchina virtuale nel passaggio precedente.

   ![Connettersi alla VM StriIm](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. A questo punto, aprire una nuova scheda terminale per copiare il file **ojdbc8. jar** scaricato in precedenza. Usare il comando SCP seguente per copiare il file jar dal computer locale alla cartella tmp dell'istanza di StriIm in esecuzione in Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copiare il file jar dal computer del percorso a StriIm](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Tornare quindi alla finestra in cui è stato effettuato SSH per l'istanza di StriIm e accedere come sudo. Spostare il file **ojdbc8. jar** dalla directory **tmp** alla directory **lib** dell'istanza di StriIm con i comandi seguenti:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Spostare il file jar nella cartella lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Dalla stessa finestra del terminale riavviare il server StriIm eseguendo i comandi seguenti:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. L'avvio di StriIm può richiedere un minuto. Se si desidera visualizzare lo stato, eseguire il comando seguente: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Tornare quindi ad Azure e copiare l'indirizzo IP pubblico della VM StriIm. 

   ![Copiare l'indirizzo IP della macchina virtuale StriIm](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Per passare all'interfaccia utente Web di StriIm, aprire una nuova scheda in un browser e copiare l'indirizzo IP pubblico seguito da: 9080. Accedere usando il nome utente **amministratore** , insieme alla password di amministratore specificata nella portale di Azure.

   ![Accedere a StriIm](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. A questo punto si arriverà alla home page di StriIm. Sono disponibili tre diversi riquadri: **Dashboard**, **app**e **SourcePreview**. Il riquadro dashboard consente di spostare i dati in tempo reale e visualizzarli. Il riquadro app contiene le pipeline di dati di streaming o i flussi di dati. Nella parte destra della pagina è SourcePreview, in cui è possibile visualizzare in anteprima i dati prima di trasferirli.

1. Selezionare il riquadro **app** per il momento. Sono disponibili diverse app di esempio che è possibile usare per acquisire familiarità con StriIm. Tuttavia, in questo articolo verranno creati dei propri. Selezionare il pulsante **Aggiungi app** nell'angolo superiore destro.

   ![Aggiungere l'app StriIm](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Esistono diversi modi per creare applicazioni StriIm. Selezionare **inizia con modello** per iniziare con un modello esistente.

   ![Avviare l'app con il modello](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. Nel campo **Cerca modelli** Digitare "Cosmos" e selezionare **destinazione: Azure Cosmos DB** quindi selezionare **Oracle CDC per Azure Cosmos DB**.

   ![Selezionare Oracle CDC per Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Nella pagina successiva assegnare un nome all'applicazione. È possibile specificare un nome, ad esempio **oraToCosmosDB** , e quindi selezionare **Save (Salva**).

1. Immettere quindi la configurazione di origine dell'istanza di Oracle di origine. Immettere un valore per il **nome dell'origine**. Il nome di origine è semplicemente una convenzione di denominazione per l'applicazione StriIm, è possibile usare un elemento come **src_onPremOracle**. Immettere i valori per Rest dell' **URL**dei parametri di origine, **username**, **password**, scegliere **LogMiner** come Reader per leggere i dati da Oracle. Selezionare **Avanti** per continuare.

   ![Configurare i parametri di origine](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. StriIm controllerà l'ambiente e assicurerà che possa connettersi all'istanza di Oracle di origine, disporre dei privilegi corretti e che CDC è stato configurato correttamente. Una volta convalidati tutti i valori, fare clic su **Avanti**.

   ![Convalida parametri di origine](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Selezionare le tabelle del database Oracle di cui si desidera eseguire la migrazione. Ad esempio, è possibile scegliere la tabella Orders e fare clic su **Next**. 

   ![Selezionare le tabelle di origine](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Dopo aver selezionato la tabella di origine, è possibile eseguire operazioni più complesse, ad esempio il mapping e il filtro. In questo caso, si creerà solo una replica della tabella di origine in Azure Cosmos DB. Quindi, selezionare **Avanti** per configurare la destinazione

1. A questo punto, configurare la destinazione:

   * **Nome destinazione** : specificare un nome descrittivo per la destinazione. 
   * **Input dall'** elenco a discesa selezionare il flusso di input da quello creato nella configurazione Oracle di origine. 
   * **Raccolte**: immettere le proprietà di configurazione Azure Cosmos DB di destinazione. La sintassi delle raccolte è **sourceschema:. SourceTable, TargetDatabase. TargetContainer**. In questo esempio il valore sarà "SYSTEM. ORDERs, StriimDemo. Orders ". 
   * **AccessKey** : PrimaryKey dell'account Azure Cosmos.
   * **ServiceEndpoint** : URI dell'account Azure Cosmos, disponibile nella sezione **chiavi** del portale di Azure. 

   Selezionare **Save (Salva** ) e **Next (avanti**).

   ![Configurare i parametri di destinazione](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Si arriverà quindi alla finestra di progettazione di Flow, in cui è possibile trascinare e rilasciare i connettori di box per creare le applicazioni di streaming. A questo punto non si apporteranno modifiche al flusso. quindi, procedere e distribuire l'applicazione selezionando il pulsante **Distribuisci app** .
 
   ![Distribuire l'app](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. Nella finestra distribuzione è possibile specificare se si desidera eseguire determinate parti dell'applicazione in parti specifiche della topologia di distribuzione. Poiché è in esecuzione una semplice topologia di distribuzione tramite Azure, verrà usata l'opzione predefinita.

   ![Usare l'opzione predefinita](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Dopo la distribuzione, è possibile visualizzare in anteprima il flusso per visualizzare il flusso di dati. Selezionare l'icona dell' **onda** e il bulbo oculare accanto. Selezionare il pulsante **distribuito** nella barra dei menu superiore e selezionare **Avvia app**.

   ![Avviare l'app](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Utilizzando un lettore **CDC (Change Data Capture)** , StriIm rileverà solo le nuove modifiche apportate al database. Se è presente un flusso di dati nelle tabelle di origine, verrà visualizzato. Tuttavia, poiché si tratta di una tabella demo, l'origine non è connessa ad alcuna applicazione. Se si utilizza un generatore di dati di esempio, è possibile inserire una catena di eventi nel database Oracle.

1. Il flusso di dati verrà visualizzato attraverso la piattaforma StriIm. StriIm preleva anche tutti i metadati associati alla tabella, che risulta utile per monitorare i dati e assicurarsi che i dati siano presenti nella destinazione corretta.

   ![Configurare la pipeline CDC](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Infine, accedere ad Azure e passare all'account Azure Cosmos. Aggiornare il Esplora dati. è possibile vedere che i dati sono arrivati.  

   ![Convalidare i dati migrati in Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Con la soluzione StriIm in Azure è possibile eseguire la migrazione continua dei dati in Azure Cosmos DB da diverse origini, ad esempio Oracle, Cassandra, MongoDB e altre ancora, per Azure Cosmos DB. Per eventuali problemi durante la configurazione del percorso di migrazione con StriIm, archiviare una richiesta di supporto nel [sito Web di StriIm](https://go2.striim.com/request-support-striim).

## <a name="next-steps"></a>Passaggi successivi

* Se si esegue la migrazione dei dati a Azure Cosmos DB API SQL, vedere [How to Migrate data to API Cassandra account using StriIm](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitorare ed eseguire il debug dei dati con Azure Cosmos DB metriche](use-metrics.md)