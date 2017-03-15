---
title: SDK e API .NET Core di DocumentDB | Documentazione Microsoft
description: Tutte le informazioni sull&quot;SDK e sull&quot;API .NET Core, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB .NET Core SDK.
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/22/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: e37416268802b849f6be350a8d7e0e6b284e830b
ms.openlocfilehash: a2972c7c7fead727e6147212b211d102faf01a29
ms.lasthandoff: 02/22/2017


---
# <a name="documentdb-apis-and-sdks"></a>SDK e API di DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-core-api-and-sdk"></a>SDK e API di DocumentDB .NET Core
<table>

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Attività iniziali**</td><td>[Introduzione a DocumentDB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

DocumentDB .NET Core SDK ha le stesse funzionalità delle versione più recente di [DocumentDB .NET SDK](documentdb-sdk-dotnet.md).

> [!NOTE] 
> DocumentDB .NET Core SDK non è ancora compatibile con le app della piattaforma UWP (Universal Windows Platform). In caso di interesse a .NET Core SDK che supporta le app della piattaforma UWP, inviare un messaggio di posta elettronica a [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.0)

* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](documentdb-sql-query.md#Aggregates).
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

DocumentDB .NET Core SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux. La versione più recente di DocumentDB .NET Core SDK è completamente compatibile con [Xamarin](https://www.xamarin.com) e può essere usata per compilare applicazioni destinate a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

DocumentDB .NET Core Preview SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux.

DocumentDB .NET Core Preview SDK ha le stesse funzionalità della versione più recente di [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) e supporta quanto segue:
* Tutte le [modalità di connessione](documentdb-performance-tips.md#networking): modalità gateway, TCP diretto e HTTP diretti. 
* Tutti i [livelli di coerenza](documentdb-consistency-levels.md): assoluta, di sessione, con decadimento ristretto e finale.
* [Raccolte partizionate](documentdb-partition-data.md). 
* [Account di database tra più aree e replica geografica](documentdb-distribute-data-globally.md).

Per domande su questo SDK, pubblicare un post su [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o inserire la domanda nel [repository github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.1.0](#1.1.0) |16 febbraio 2017 |--- |
| [1.0.0](#1.0.0) |21 dicembre 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 novembre 2016 |31 dicembre 2016 |

## <a name="see-also"></a>Vedere anche
Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 


