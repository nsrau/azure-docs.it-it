---
title: Connettere un'applicazione MongoDB ad Azure Cosmos DB
description: Informazioni su come connettere un'app MongoDB a Azure Cosmos DB ottenendo la stringa di connessione dal portale di AzureLearn how to connect a MongoDB app to Azure Cosmos DB by getting the connection string from Azure portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617045"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connettere un'applicazione MongoDB ad Azure Cosmos DB

Informazioni su come connettere un'app MongoDB ad Azure Cosmos DB usando una stringa di connessione MongoDB. È quindi possibile usare un database di Azure Cosmos DB come archivio dati per l'app MongoDB.

Questa esercitazione illustra due modi per recuperare le informazioni della stringa di connessione:

- [Metodo di avvio rapido](#get-the-mongodb-connection-string-by-using-the-quick-start), per l'uso con i driver .NET, Node.js, MongoDB Shell, Java e Python
- [Il metodo della stringa](#get-the-mongodb-connection-string-to-customize)di connessione personalizzata , da utilizzare con altri driver

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si ha un account Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) ora.
- Un account Cosmos. Per istruzioni, vedere [Creare un'app Web usando l'API Azure Cosmos DB per MongoDB e .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Ottenere la stringa di connessione MongoDB usando il metodo di avvio rapido

1. In un browser Internet accedere al portale di [Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API.
3. Nel riquadro sinistro del pannello dell'account fare clic su **Avvio rapido**.
4. Scegliere la piattaforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Se il driver o lo strumento non compare nell'elenco, ricordare che altri frammenti di codice di connessione vengono continuamente documentati. Commentare di seguito ciò che si vorrebbe vedere. Per informazioni su come realizzare la propria connessione, leggere [Ottenere informazioni sulla stringa di connessione dell'account](#get-the-mongodb-connection-string-to-customize).
5. Copiare e incollare il frammento di codice nell'app MongoDB.

    ![Pannello di avvio rapido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a> Ottenere la stringa di connessione MongoDB da personalizzare

1. In un browser Internet accedere al portale di [Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API.
3. Nel riquadro sinistro del pannello dell'account, fare clic su **Stringa di connessione**.
4. Viene aperto il pannello **Stringa di connessione**. Contiene tutte le informazioni necessarie per connettersi all'account usando un driver per MongoDB, inclusa una stringa di connessione precostruita.

   [![Lama](./media/connect-mongodb-account/ConnectionStringBlade.png) Stringa di connessione](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Requisiti della stringa di connessione

> [!Important]
> Azure Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Gli account di Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite *TLS*. 
>
>

Il database Cosmos di Azure supporta il formato URI della stringa di connessione MongoDB standard, con un paio di requisiti specifici: gli account di Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite TLS. Il formato della stringa di connessione sarà quindi:

    mongodb://username:password@host:port/[database]?ssl=true

I valori di questa stringa sono disponibili nel pannello **Stringa di connessione** mostrato in precedenza:

* Nome utente (obbligatorio): nome dell'account Cosmos.
* Password (obbligatorio): password dell'account Cosmos.
* Host (obbligatorio): FQDN dell'account Cosmos.
* Porta (obbligatorio): 10255.
* Database (facoltativo): il database utilizzato dalla connessione. Se viene specificato alcun database, il database predefinito è "test".
* ssl=true (obbligatorio)

Si consideri l'account mostrato nel pannello **Stringa di connessione**. Una stringa di connessione valida è la seguente:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
