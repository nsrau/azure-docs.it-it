---
title: Risorse, SDK e API del processore dei feed delle modifiche .NET per Azure Cosmos DB | Microsoft Docs
description: Tutte le informazioni sull'SDK e sull'API del processore dei feed delle modifiche, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni dell'SDK del processore dei feed delle modifiche .NET.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2018
ms.author: maquaran
ms.openlocfilehash: 7e53cf67bc6e03a5f45de5ad4e23ff91624874f4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK del processore dei feed delle modifiche .NET: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentazione sull'API**|[Documentazione di riferimento sull'API della libreria del processore dei feed delle modifiche](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introduzione**|[Introduzione all'SDK del processore dei feed delle modifiche .NET](change-feed.md)|
|**Framework attualmente supportato**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Miglioramenti della stabilità.
* Supporto per la creazione di checkpoint manuale.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 e versioni successive.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Aggiunge il supporto per .NET Standard 2.0. Il pacchetto ora supporta i moniker framework `netstandard2.0` e `net451`.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 e versioni successive.
* Compatibile con [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 e versioni successive.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Correzione di un problema relativo al calcolo della stima del lavoro rimanente quando il feed di modifiche è vuoto o non ci sono lavori in sospeso.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Aggiungere un metodo per ottenere una stima di quanto resta da elaborare nel feed delle modifiche.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 e versioni precedenti.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.3.0](#1.3.0) |7 marzo 2018 |--- |
| [1.2.0](#1.2.0) |31 ottobre 2017 |--- |
| [1.1.1](#1.1.1) |29 agosto 2017 |--- |
| [1.1.0](#1.1.0) |13 agosto 2017 |--- |
| [1.0.0](#1.0.0) |07 luglio 2017 |--- |


## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche 
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

