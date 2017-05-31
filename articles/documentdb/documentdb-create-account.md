---
title: Come creare un account Azure Cosmos DB | Microsoft Docs
description: Compilare un database NoSQL con Azure Cosmos DB. Seguire queste istruzioni per creare un account Azure Cosmos DB e iniziare a compilare un database NoSQL estremamente veloce su scala mondiale.
keywords: Compilare un database
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: mimig
redirect_url: https://aka.ms/acdbnetqa
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 13621d942f2880f4dd1523315f43099eca2607d8
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-nosql-account-using-the-azure-portal"></a>Come creare un account Azure Cosmos DB NoSQL usando il portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](documentdb-create-account.md)
> * [Interfaccia della riga di comando di Azure 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Per compilare un database con Microsoft Azure Cosmos DB è necessario quanto segue:

* Avere un account Azure. È possibile ottenere un [account Azure gratuito](https://azure.microsoft.com/free) se non se ne ha già uno.
* Creare un account Azure Cosmos DB.  

Per creare un account Azure Cosmos DB è possibile usare il portale di Azure, i modelli di Azure Resource Manager o l'interfaccia della riga di comando di Azure. Questo articolo illustra come creare un account Azure Cosmos DB usando il portale di Azure. Per creare un account con Azure Resource Manager o l'interfaccia della riga di comando di Azure, vedere [Automatizzare la creazione dell'account database di Azure Cosmos DB](documentdb-automation-resource-manager-cli.md).

Se non si ha familiarità con Azure Cosmos DB, è possibile guardare [questo](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video di quattro minuti a cura di Scott Hanselman per vedere come eseguire le attività più comuni nel portale online.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel riquadro di spostamento a sinistra fare clic su **Nuovo**, quindi su **Database** e su **Azure Cosmos DB**.

   ![Screenshot del portale di Azure in cui sono evidenziati Altri servizi e NoSQL (Azure Cosmos DB)](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. Nel pannello **Nuovo account** specificare la configurazione desiderata per l'account Azure Cosmos DB.

    ![Screenshot del pannello Nuovo Azure Cosmos DB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * Nella casella **ID** immettere un nome identificativo per l'account Azure Cosmos DB.  Quando l'**ID** viene convalidato, nella casella **ID** viene visualizzato un segno di spunta verde. Il valore **ID** diventa il nome host all'interno dell'URI. L' **ID** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Si noti che al nome dell'endpoint scelto viene aggiunto *documents.azure.com* e il risultato finale sarà l'endpoint dell'account Azure Cosmos DB.
   * Nella casella **NoSQL API** (API NoSQL) selezionare il modello di programmazione da usare:

     * **DocumentDB**: l'API DocumentDB è disponibile tramite gli [SDK](documentdb-sdk-dotnet.md) .NET, Java, Node.js, Python e JavaScript, oltre che tramite [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) HTTP, e consente l'accesso a livello di codice a tutte le funzionalità di DocumentDB.
     * **MongoDB**: DocumentDB offre anche [supporto a livello di protocollo](documentdb-protocol-mongodb.md) per le API **MongoDB**. Quando si sceglie l'opzione API MongoDB, è possibile usare gli SDK MongoDB e gli [strumenti](documentdb-mongodb-mongochef.md) esistenti per comunicare con DocumentDB. È possibile [spostare](documentdb-import-data.md) le app MongoDB esistenti per usare DocumentDB, [senza dover apportare modifiche al codice](documentdb-connect-mongodb-account.md), e sfruttare i vantaggi di un database completamente gestito distribuito come servizio, con scalabilità illimitata, replica globale e altre funzionalità.
   * In **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. Se l'account ha solo una sottoscrizione, viene selezionato per impostazione predefinita.
   * In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account Azure Cosmos DB.  Per impostazione predefinita, viene creato un nuovo gruppo di risorse. Per altre informazioni, vedere [Gestire le risorse di Azure mediante il portale](../azure-portal/resource-group-portal.md).
   * Usare **Località** per specificare l'area geografica in cui verrà ospitato l'account Azure Cosmos DB.
4. Dopo aver configurato le opzioni del nuovo account Azure Cosmos DB, fare clic su **Crea**. Per controllare lo stato della distribuzione, controllare l'hub Notifiche.  

   ![Creare database in modo rapido: screenshot dell'hub Notifiche, che mostra l'account Azure Cosmos DB mentre viene creato](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Screenshot dell'hub Notifiche, che mostra che l'account Azure Cosmos DB è stato creato correttamente e distribuito in un gruppo di risorse: notifica del servizio di creazione database online](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. Dopo la creazione, l'account Azure Cosmos DB è pronto per l'uso con le impostazioni predefinite. La coerenza predefinita dell'account Azure Cosmos DB è impostata su **Sessione**.  Per modificarla è possibile fare clic su **Coerenza predefinita** nel menu della risorsa. Per altre informazioni sui livelli di coerenza offerti da Azure Cosmos DB, vedere [Livelli di coerenza in Azure Cosmos DB](documentdb-consistency-levels.md).

   ![Screenshot del pannello Gruppo di risorse: inizio dello sviluppo dell'applicazione](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Screenshot del pannello del livello di coerenza: coerenza di sessione](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create an Azure Cosmos DB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Passaggi successivi
Ora che è stato creato l'account Azure Cosmos DB, il passaggio successivo consiste nel creare una raccolta e un database di Azure Cosmos DB.

Per creare un nuovo database e una nuova raccolta è possibile usare uno dei metodi seguenti:

* Il portale di Azure, come descritto in [Creare una raccolta Azure Cosmos DB usando il portale di Azure](documentdb-create-collection.md).
* Le esercitazioni complete, che includono dati di esempio: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) o [Python](documentdb-python-application.md).
* Il codice di esempio [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples) o [Python](documentdb-python-samples.md#database-examples) disponibile in GitHub.
* Gli SDK [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md) e [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx).

Dopo aver creato il database e la raccolta, è necessario [aggiungere i documenti](documentdb-view-json-document-explorer.md) alle raccolte.

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#ExecutingSqlQueries) sui documenti tramite [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Altre informazioni
Per altre informazioni su Azure Cosmos DB, vedere le risorse seguenti:

* [Introduzione ad Azure Cosmos DB](../cosmos-db/introduction.md)

