---
title: Risorse ed SDK del processore dei feed delle modifiche per Azure DocumentDB .NET | Microsoft Docs
description: Tutte le informazioni sull'SDK e sull'API del processore dei feed delle modifiche, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni dell'SDK del processore dei feed delle modifiche di DocumentDB .NET.
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
ms.date: 08/29/2017
ms.author: maquaran
ms.openlocfilehash: 239b590a1e3a83fe0205dd8169697db745d7f75e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>SDK del processore dei feed delle modifiche di DocumentDB .NET: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed delle modifiche .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento sull'API della libreria del processore dei feed delle modifiche](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione all'SDK del processore dei feed delle modifiche di DocumentDB .NET](change-feed.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Correzione di un problema relativo al calcolo della stima del lavoro rimanente quando il feed di modifiche è vuoto o non ci sono lavori in sospeso.
* Compatibile con [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Aggiungere un metodo per ottenere una stima di quanto resta da elaborare nel feed delle modifiche.
* Compatibile con [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale
* Compatibile con [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.14.1 e versioni precedenti.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.1.1](#1.1.1) |29 agosto 2017 |--- |
| [1.1.0](#1.1.0) |13 agosto 2017 |--- |
| [1.0.0](#1.0.0) |07 luglio 2017 |--- |


## <a name="faq"></a>domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

