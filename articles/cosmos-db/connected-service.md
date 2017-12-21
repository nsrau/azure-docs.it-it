---
title: Servizio connesso di Visual Studio per Azure Cosmos DB
description: Consente agli sviluppatori di connettere facilmente l'account Azure Cosmos DB e di gestire le risorse tramite Servizi connessi di Visual Studio
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: f63c20831a2700f1024507bef614a5a7cc87d00e
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: servizio connesso di Visual Studio (anteprima)

Servizi connessi di Visual Studio consente agli sviluppatori di connettere facilmente l'account Azure Cosmos DB e di gestire le risorse.

È anche possibile usare Esplora dati nel servizio connesso per creare stored procedure, funzioni definite dall'utente e trigger per eseguire la logica di business sul lato server. Esplora dati espone tutti i tipi di accesso ai dati a livello di codice predefiniti disponibili nelle API, ma consente anche di accedere facilmente ai dati.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di avere quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/). 
* Account Azure Cosmos DB. Se non è già presente, seguire la procedura illustrata in [Creazione di un account Azure Cosmos DB](create-sql-api-dotnet.md) per crearne uno nel portale di Azure o vedere [Creare un account Azure Cosmos DB nello strumento di servizio connesso](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Per usare un ambiente locale a fini di sviluppo, è possibile usare l'[emulatore di Azure Cosmos DB](local-emulator.md). L'ambiente emula il servizio Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/)
* Bit più recenti del servizio connesso Azure Cosmos DB. È possibile scaricare il servizio connesso Azure Cosmos DB dal marketplace di Visual Studio, come illustrato nello screenshot seguente. Aprire **Visual Studio** nel computer. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti** e quindi scegliere **Online** / **Visual Studio Marketplace**. Immettere **cosmosdb** per cercare i bit.

    È anche possibile installare il servizio connesso Azure Cosmos DB da [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Screenshot del download del servizio connesso (bits.png)](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>Configurare la soluzione di Visual Studio
1. Aprire **Visual Studio** nel computer.
2. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
3. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#** / **App console (.NET Framework)** o **App WPF (.NET Framework)**, assegnare un nome al progetto e quindi fare clic su **OK**.

    ![Screenshot della finestra Nuovo progetto](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Aggiungere il servizio connesso e l'account
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo di progetto, quindi scegliere **Aggiungi** / **Servizio connesso** oppure scegliere **Aggiungi servizio connesso** dal menu **Progetto**.

    ![Screenshot della finestra Aggiungi servizio connesso](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Nella pagina del servizio connesso fare clic su **Servizi connessi** / **Azure Cosmos DB** per aprire la pagina **Azure Cosmos DB**.

    ![Screenshot della finestra Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Fare clic sulla freccia giù per eseguire il primo accesso o aggiungere un account. Dopo l'accesso, tutti gli account Azure Cosmos DB vengono visualizzati nell'area vuota. Scegliere un account Azure Cosmos DB da aggiungere al progetto.

    ![Screenshot della finestra di accesso con l'account database elencato](./media/connected-service/connected-service-add-db-account.png)
4. Dopo avere aggiunto un account Azure Cosmos DB, al progetto viene aggiunta una cartella di servizi connessi per l'account Azure Cosmos DB. È possibile aggiungere più di un account Azure Cosmos DB ripetendo i passaggi da 1 a 3.

    ![Screenshot della finestra della cartella di servizi connessi](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Dopo avere aggiunto un servizio connesso Azure Cosmos DB, modificare il progetto per consentire l'accesso ad Azure Cosmos DB in uno dei modi seguenti:

    * Vengono installati alcuni pacchetti NuGet necessari per il client Azure Cosmos DB. È possibile visualizzarli dal file di configurazione dei pacchetti. 

        ![Nuova configurazione dei pacchetti Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * La chiave e l'URI di connessione di Azure Cosmos DB vengono aggiunti al file di configurazione del progetto, in questo caso, App.config. 

        ![Nuova configurazione dell'app Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Aprire l'utilità di esplorazione di Azure Cosmos DB
1. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Open Cosmos DB Explorer...** (Apri Esplora Cosmos DB).

    ![Screenshot del tentativo di aprire la finestra Esplora dati](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Nella pagina **Choose a Cosmos DB Account** (Scegliere un account Cosmos DB) fare clic sull'elenco a discesa per selezionare un account Azure Cosmos DB.

    ![Screenshot della finestra con l'aggiunta servizio connesso per l'account](./media/connected-service/connected-service-open-explorer.png)
3. Fare clic su **Apri** per visualizzare la finestra Esplora dati.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Creare un account Azure Cosmos DB nello strumento di servizio connesso
1. Nella parte inferiore del riquadro della pagina del servizio connesso fare clic su **Create a New Cosmos DB Account** (Crea un nuovo account Cosmos DB) per aprire la pagina **Crea account Cosmos DB**.

    ![Screenshot della finestra del punto di ingresso per la creazione dell'account Azure Cosmos DB aperta](./media/connected-service/connected-service-click-new-db-account.png)
2. Nella pagina **Crea account Cosmos DB** specificare la configurazione desiderata per l'account Azure Cosmos DB.

    Completare i campi nella pagina **Crea account Cosmos DB** usando le informazioni riportate nello screenshot seguente come riferimento. 
 
    ![Nuova pagina di Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Fare clic su **Crea** per creare l'account.

## <a name="use-data-explorer"></a>Usare Esplora dati

Dopo avere aperto Esplora dati, è possibile eseguire queste operazioni:
* Creare ed eliminare database
* Creare ed eliminare raccolte
* Creare, eliminare e filtrare documenti
* Creare ed eliminare stored procedure
* Creare ed eliminare trigger e funzioni definite dall'utente per eseguire la logica di business lato server. 

![Nuova pagina di Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demo

Guardare il video seguente per informazioni su come usare il servizio connesso Azure Cosmos DB in Visual Studio: [Use Azure Cosmos DB Connected Service in Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711) (Usare il servizio connesso Azure Cosmos DB in Visual Studio)

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come:

> [!div class="checklist"]
> * Creare un account Azure Cosmos DB
> * Aggiungere il servizio connesso e l'account
> * Aprire l'utilità di esplorazione di Azure Cosmos DB
> * Usare Esplora dati

Ora che Servizi connessi è operativo con l'account Azure Cosmos DB, passare a una delle esercitazioni per iniziare a sviluppare la soluzione:

* [Sviluppare con l'API SQL in .NET](tutorial-develop-sql-api-dotnet.md).
* [Azure Cosmos DB: Esercitazione introduttiva per l'API SQL](sql-api-get-started.md).
* Per la scalabilità e i test delle prestazioni con Azure Cosmos DB, Vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Informazioni su come [monitorare un account Azure Cosmos DB](monitor-accounts.md).

