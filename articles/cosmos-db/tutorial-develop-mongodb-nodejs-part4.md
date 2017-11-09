---
title: Esercitazione su MongoDB, Angular e Node per Azure - Parte 4 | Microsoft Docs
description: Parte 4 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 20c2ae547d794836474aa34096a20de25dad96c8
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Creare un'app MongoDB con Angular e Azure Cosmos DB - Parte 4: Creare un account Azure Cosmos DB usando l'interfaccia della riga di comando di Azure

Questa esercitazione in più parti illustra come creare una nuova app per le [API MongoDB](mongodb-introduction.md) scritta in Node.js con Express e Angular e il database di Azure Cosmos DB.

La Parte 4 dell'esercitazione è basata sulla [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un gruppo di risorse di Azure con l'interfaccia della riga di comando di Azure
> * Creare un account Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa parte dell'esercitazione, assicurarsi di avere completato le procedure illustrate nella [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) dell'esercitazione. 

In questa sezione dell'esercitazione è possibile usare Azure Cloud Shell (nel browser Internet) o l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installata in locale. Se si usa l'interfaccia della riga di comando di Azure in locale, assicurarsi di eseguire l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` al prompt dei comandi per controllare la versione in uso. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare un account Azure Cosmos DB con il comando [`az cosmosdb create`](/cli/azure/cosmosdb#create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Per `<cosmosdb-name>` usare il nome account Azure Cosmos DB univoco, che deve essere univoco tra tutti i nomi account Azure Cosmos DB in Azure.
* L'impostazione `--kind MongoDB` consente ad Azure Cosmos DB di avere connessioni client MongoDB.

Il completamento del comando potrebbe richiedere un minuto o due. Al termine, la finestra del terminale visualizza le informazioni sul nuovo database. 

Dopo la creazione dell'account Azure Cosmos DB:
1. Aprire una nuova finestra del browser e passare a [https://portal.azure.com](https://portal.azure.com)
1. Fare clic sul logo di Azure Cosmos DB ![Icona di Azure Cosmos DB nel portale di Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) nella barra a sinistra per visualizzare tutte le istanze di Azure Cosmos DB disponibili.
1. Fare clic sull'account Azure Cosmos DB appena creato, selezionare la scheda **Panoramica** e scorrere verso il basso per visualizzare la mappa con la posizione del database. 

    ![Nuovo account Azure Cosmos DB nel portale di Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Scorrere verso il basso sulla barra di spostamento a sinistra e fare clic sulla scheda **Replica i dati a livello globale** per visualizzare una mappa con le diverse aree in cui è possibile eseguire la replica. È ad esempio possibile fare clic su Australia sud-orientale o su Australia orientale e replicare i dati in Australia. Per altre informazioni sulla replica globale, vedere [Come distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md). Per il momento sarà sufficiente una sola istanza che sarà possibile replicare, se necessario.

    ![Nuovo account Azure Cosmos DB nel portale di Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato creato un gruppo di risorse di Azure con l'interfaccia della riga di comando di Azure
> * È stato creato un account Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure

È possibile passare alla parte successiva dell'esercitazione per connettere Azure Cosmos DB all'app usando Mongoose.

> [!div class="nextstepaction"]
> [Usare Mongoose per connettersi ad Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
