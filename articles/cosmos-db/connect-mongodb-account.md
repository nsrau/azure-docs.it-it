---
title: Stringa di connessione MongoDB per un account Azure Cosmos DB
description: Informazioni su come connettere l'app MongoDB a un account Azure Cosmos DB usando una stringa di connessione MongoDB.
keywords: stringa di connessione mongodb
services: cosmos-db
author: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: a78a77e16e9a810c0be03656aa48b02cc8e6e5e6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849259"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connettere un'applicazione MongoDB ad Azure Cosmos DB
Informazioni su come connettere l'app MongoDB a un account Azure Cosmos DB usando una stringa di connessione MongoDB. È quindi possibile usare un database di Azure Cosmos DB come archivio dati per l'app MongoDB. 

Questa esercitazione illustra due modi per recuperare le informazioni della stringa di connessione:

- [Metodo di avvio rapido](#QuickstartConnection), per l'uso con i driver .NET, Node.js, MongoDB Shell, Java e Python
- [Metodo basato su stringa di connessione personalizzata](#GetCustomConnection), da usare con altri driver

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si ha un account Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) ora. 
- Account Azure Cosmos DB. Per istruzioni, vedere [Creare un'app Web API MongoDB con .NET e il portale di Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Ottenere la stringa di connessione MongoDB usando il metodo di avvio rapido
1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API per l'account MongoDB. 
3. Nel riquadro sinistro del pannello dell'account fare clic su **Avvio rapido**. 
4. Scegliere la piattaforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Se il driver o lo strumento non compare nell'elenco, ricordare che altri frammenti di codice di connessione vengono continuamente documentati. Commentare di seguito ciò che si vorrebbe vedere. Per informazioni su come realizzare la propria connessione, leggere [Ottenere informazioni sulla stringa di connessione dell'account](#GetCustomConnection).
5. Copiare e incollare il frammento di codice nell'app MongoDB.

    ![Pannello di avvio rapido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Ottenere la stringa di connessione MongoDB da personalizzare
1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API per l'account MongoDB. 
3. Nel riquadro sinistro del pannello dell'account, fare clic su **Stringa di connessione**. 
4. Viene aperto il pannello **Stringa di connessione**. Contiene tutte le informazioni necessarie per connettersi all'account usando un driver per MongoDB, inclusa una stringa di connessione precostruita.

    ![Pannello Stringa di connessione](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisiti della stringa di connessione
> [!Important]
> Azure Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite *SSL*. 
>
>

Azure Cosmos DB supporta il formato URI della stringa di connessione MongoDB standard, con un paio di requisiti specifici: gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite SSL. Il formato della stringa di connessione sarà quindi:

    mongodb://username:password@host:port/[database]?ssl=true

I valori di questa stringa sono disponibili nel pannello **Stringa di connessione** mostrato in precedenza:

* Nome utente (obbligatorio): nome dell'account Azure Cosmos DB.
* Password (obbligatoria): password dell'account Azure Cosmos DB.
* Host (obbligatorio): nome di dominio completo dell'account Azure Cosmos DB.
* Porta (obbligatoria): 10255.
* Database (facoltativo): database usato dalla connessione. Se viene specificato alcun database, il database predefinito è "test".
* ssl=true (obbligatorio)

Si consideri l'account mostrato nel pannello **Stringa di connessione**. Una stringa di connessione valida è la seguente:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare Studio 3T (MongoChef)](mongodb-mongochef.md) con un'API di Azure Cosmos DB per l'account MongoDB.
* Esaminare l'API di Azure Cosmos DB per MongoDB visualizzando gli [esempi](mongodb-samples.md).
