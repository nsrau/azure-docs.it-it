---
title: Distribuire un'app Web con un modello - Azure Cosmos DB
description: Informazioni su come distribuire un account Azure Cosmos, app Azure app Web del servizio e un'applicazione Web di esempio usando un modello di Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 5038d9968e37b956774d1c5f8abdb14865422e8b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027743"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Distribuire Azure Cosmos DB e app Azure servizio con un'app Web da GitHub usando un modello di Azure Resource Manager

In questa esercitazione viene illustrato come eseguire una distribuzione "No Touch" di un'applicazione Web che si connette a Azure Cosmos DB alla prima esecuzione senza dover tagliare e incollare le informazioni di connessione da Azure Cosmos DB a `appsettings.json` o alle impostazioni dell'applicazione servizi app Azure nel portale di Azure. Tutte queste azioni vengono eseguite utilizzando un modello di Azure Resource Manager in un'unica operazione. Nell'esempio seguente viene illustrato come distribuire l' [esempio Azure Cosmos DB todo](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) da un' [app Web](sql-api-dotnet-application.md).

Gestione risorse modelli sono piuttosto flessibili e consentono di comporre distribuzioni complesse in qualsiasi servizio di Azure. Sono incluse attività avanzate, ad esempio la distribuzione di applicazioni da GitHub e l'inserimento di informazioni di connessione nelle impostazioni dell'applicazione del servizio app Azure nel portale di Azure. In questa esercitazione viene illustrato come eseguire le operazioni seguenti utilizzando un singolo modello di Gestione risorse.

* Distribuire un account Azure Cosmos.
* Distribuire un piano di hosting del servizio app Azure.
* Distribuire un servizio app Azure.
* Inserire l'endpoint e le chiavi dall'account Azure Cosmos nelle impostazioni dell'applicazione del servizio app nel portale di Azure.
* Distribuire un'applicazione Web da un repository GitHub al servizio app.

La distribuzione risultante ha un'applicazione Web completamente funzionante che può connettersi a Azure Cosmos DB senza dover tagliare e incollare l'URL dell'endpoint Azure Cosmos DB o le chiavi di autenticazione dal portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

> [!TIP]
> Anche se questa esercitazione non presuppone alcuna esperienza nell'uso di JSON o dei modelli di Azure Resource Manager, se si vogliono modificare i modelli o le opzioni di distribuzione cui viene fatto riferimento, è necessario conoscere ciascuna di queste aree.

## <a name="step-1-deploy-the-template"></a>Passaggio 1: distribuire il modello

Per prima cosa, selezionare il pulsante **Distribuisci in Azure** per aprire il portale di Azure per creare una distribuzione personalizzata. È anche possibile visualizzare il modello di Azure Resource Manager dalla [raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Una volta nel portale di Azure, selezionare la sottoscrizione in cui eseguire la distribuzione e selezionare o creare un nuovo gruppo di risorse. Inserire quindi i valori seguenti.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Screenshot dell'interfaccia utente della distribuzione del modello":::

* **Region** : questa operazione è richiesta dal gestione risorse. Immettere la stessa area usata dal parametro location in cui si trovano le risorse.
* **Nome applicazione** : questo nome viene usato da tutte le risorse per la distribuzione. Assicurarsi di scegliere un nome univoco per evitare conflitti con gli account di Azure Cosmos DB e del servizio app esistenti.
* **Località** : area in cui vengono distribuite le risorse.
* Livello del **piano di servizio app** -piano tariffario del piano di servizio app.
* **Istanze del piano di servizio app** : numero di processi di lavoro per il piano di servizio app.
* **URL del repository** : repository dell'applicazione Web su GitHub.
* **Branch** : ramo per il repository GitHub.
* **Nome database** : nome del database di Azure Cosmos.
* **Nome del contenitore** : il nome del contenitore di Azure Cosmos.

Dopo aver compilato i valori, selezionare il pulsante **Crea** per avviare la distribuzione. Il completamento di questo passaggio può richiedere da 5 a 10 minuti.

> [!TIP]
> Il modello non verifica che il nome del servizio app Azure e il nome dell'account Azure Cosmos immessi nel modello siano validi e disponibili. È consigliabile verificare la disponibilità dei nomi che si intende fornire prima della distribuzione.


## <a name="step-2-explore-the-resources"></a>Passaggio 2: esplorare le risorse

### <a name="view-the-deployed-resources"></a>Visualizzare le risorse distribuite

Dopo che il modello ha distribuito le risorse, è ora possibile visualizzarle ognuna nel gruppo di risorse.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Gruppo di risorse":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Visualizza Cosmos DB endpoint e le chiavi

Aprire quindi l'account Azure Cosmos nel portale. La schermata seguente mostra l'endpoint e le chiavi per un account Azure Cosmos.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Chiavi Cosmos":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Visualizzare le chiavi Azure Cosmos DB nelle impostazioni dell'applicazione

Passare quindi al servizio app Azure nel gruppo di risorse. Fare clic sulla scheda configurazione per visualizzare le impostazioni dell'applicazione per il servizio app. Le impostazioni dell'applicazione contengono l'account Cosmos DB e i valori di chiave primaria necessari per connettersi ai Cosmos DB, nonché i nomi di database e di contenitori passati dalla distribuzione del modello.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Impostazioni applicazione":::

### <a name="view-web-app-in-deployment-center"></a>Visualizza app Web nel centro distribuzione

Passare quindi al centro distribuzione per il servizio app. Qui vengono visualizzati i punti di repository nel repository GitHub passato al modello. Lo stato seguente indica anche esito positivo (attivo), ovvero l'applicazione è stata distribuita e avviata correttamente.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Centro distribuzione":::

### <a name="run-the-web-application"></a>Eseguire l'applicazione Web

Fare clic su **Sfoglia** nella parte superiore del centro distribuzione per aprire l'applicazione Web. L'applicazione Web viene visualizzata nella schermata iniziale. Fare clic su **Crea nuovo** e immettere alcuni dati nei campi e fare clic su Salva. La schermata risultante Mostra i dati salvati in Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Schermata Home":::

## <a name="step-3-how-does-it-work"></a>Passaggio 3: funzionamento

Per il corretto funzionamento di questa soluzione sono necessari tre elementi.

### <a name="reading-app-settings-at-runtime"></a>Lettura delle impostazioni dell'app in fase di esecuzione

In primo luogo, l'applicazione deve richiedere l'endpoint Cosmos DB e la chiave nella `Startup` classe nell'applicazione Web MVC ASP.NET. Il [Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) eseguire l'esempio può essere eseguito localmente, in cui è possibile immettere le informazioni di connessione nel appsettings.js. Tuttavia, quando viene distribuito, questo file viene distribuito con l'app. Se queste righe in rosso non sono in grado di accedere alle impostazioni da appsettings.js, il tentativo verrà eseguito dalle impostazioni dell'applicazione in app Azure servizio.

:::image type="content" source="./media/create-website/startup.png" alt-text="Avvio":::

### <a name="using-special-azure-resource-management-functions"></a>Uso di funzioni speciali di gestione delle risorse di Azure

Perché questi valori siano disponibili per l'applicazione al momento della distribuzione, il modello di Azure Resource Manager può richiedere tali valori dall'account Cosmos DB usando funzioni speciali di gestione delle risorse di Azure, tra cui [Reference](../azure-resource-manager/templates/template-functions-resource.md#reference) e [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) , che acquisiscono i valori dall'account Cosmos DB e li inseriscono nei valori delle impostazioni dell'applicazione con i nomi delle chiavi che corrispondono a quelli usati nell'applicazione precedente nel formato ' {section: Key} Ad esempio: `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Chiavi modello":::

### <a name="deploying-web-apps-from-github"></a>Distribuzione di app Web da GitHub

Infine, è necessario distribuire l'applicazione Web da GitHub nel servizio app. Questa operazione viene eseguita usando il codice JSON seguente. Il tipo e il nome di questa risorsa devono essere attenti a due aspetti. Entrambi i `"type": "sourcecontrols"` `"name": "web"` valori delle proprietà e sono hardcoded e non devono essere modificati.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Eseguire la distribuzione da GitHub":::

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Sono stati distribuiti Azure Cosmos DB, app Azure servizio e un'applicazione Web di esempio che dispone automaticamente delle informazioni di connessione necessarie per connettersi a Cosmos DB, il tutto in un'unica operazione e senza dover tagliare e incollare informazioni riservate. Usando questo modello come punto di partenza, è possibile modificarlo per distribuire le proprie applicazioni Web nello stesso modo.

* Per il modello di Azure Resource Manager per questo esempio, passare alla [raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* Per il codice sorgente per l'app di esempio, passare a [Cosmos DB per eseguire l'app su GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
