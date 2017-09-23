---
title: Stringa di connessione MongoDB per un account Azure Cosmos DB | Microsoft Docs
description: Informazioni su come connettere l'app MongoDB a un account Azure Cosmos DB usando una stringa di connessione MongoDB.
keywords: stringa di connessione mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 5a47001705531d971d3181df9c0aa8f957168845
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

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

Azure Cosmos DB supporta il formato URI della stringa di connessione di MongoDB standard, con un paio di requisiti specifici: gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite SSL. Il formato della stringa di connessione sarà quindi:

    mongodb://username:password@host:port/[database]?ssl=true

I valori di questa stringa sono disponibili nel pannello **Stringa di connessione** mostrato in precedenza:

* Nome utente (obbligatorio): nome dell'account Azure Cosmos DB.
* Password (obbligatorio): password dell'account Azure Cosmos DB.
* Host (obbligatorio): nome di dominio completo dell'account Azure Cosmos DB.
* Porta (obbligatorio): 10255.
* Database (facoltativo): il database utilizzato dalla connessione. Se viene specificato alcun database, il database predefinito è "test".
* ssl=true (obbligatorio)

Si consideri l'account mostrato nel pannello **Stringa di connessione**. Una stringa di connessione valida è la seguente:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [utilizzare MongoChef](mongodb-mongochef.md) con un'API di Azure Cosmos DB per l'account MongoDB.
* Esaminare l'API di Azure Cosmos DB per MongoDB visualizzando gli [esempi](mongodb-samples.md).

