---
title: SDK e risorse .NET dell'API di tabella di CosmosDB di Azure | Documentazione Microsoft
description: Informazioni sull'API Table di Azure Cosmos DB, incluse le date di rilascio, le date di ritiro e le modifiche apportate nelle diverse versioni.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2017
ms.author: mimig
ms.openlocfilehash: 02bb5d23ee9468ab1f74396877cdcd6bdd8b8fba
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET di tabella di CosmosDB di Azure: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://aka.ms/acdbtableapiref)|
|**Guida introduttiva**|[Azure Cosmos DB: Build an app with .NET and the Table API](create-table-dotnet.md) (Cosmos DB di Azure: compilare un'app con .NET e l'API di tabella)|
|**Esercitazione**|[Azure Cosmos DB: sviluppare con l'API Table in .NET](tutorial-develop-table-dotnet.md)|
|**Framework attualmente supportato**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Se si è creato un account dell'API di tabella durante l'anteprima, creare un [nuovo account dell'API di tabella](create-table-dotnet.md#create-a-database-account) per usare gli SDK dell'API di tabella disponibili a livello generale.
>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versione con disponibilità generale

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* Versione di anteprima iniziale

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Il [Windowsazure PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) pacchetto di anteprima è stato deprecato e sostituito il [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) pacchetto. il SDK di Windowsazure PremiumTable verrà ritirato il 15 novembre 2018, in cui il tempo di richiesta per il SDK ritirato non è consentito.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata ad Azure Cosmos DB con un SDK ritirato viene rifiutata dal servizio.
<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.0.0](#1.0.0) |15 novembre 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 novembre 2017 |--- |

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si ottiene l'errore 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Quando si tenta di usare il pacchetto NuGet Microsoft.Azure.CosmosDB.Table, sono disponibili due opzioni per risolvere il problema:

* Usare la console di gestione pacchetti per installare il pacchetto Microsoft.Azure.CosmosDB.Table e le relative dipendenze. A tale scopo, digitare quanto segue nella console di gestione pacchetti per la soluzione. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Usando lo strumento di gestione pacchetti Nuget preferito, installare il pacchetto Microsoft.Azure.Storage.Common Nuget prima di installare Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche 
Per altre informazioni sull'API Table di Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB: API Table](table-introduction.md). 
