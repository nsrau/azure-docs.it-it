---
title: SDK .NET Standard e risorse relative all'API Tabella di Azure Cosmos DB
description: Informazioni sull'SDK .NET Standard e sull'API Tabella di Azure Cosmos DB, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le diverse versioni.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: c1422b6411fc60383cffa6c3082108bb940c4343
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996208"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API .NET Standard Tabella di Azure Cosmos DB: download e note sulla versione
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Esempio**|[Esempio di Cosmos DB API Tabella .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Guida introduttiva**|[Guida introduttiva](create-table-dotnet.md)|
|**Esercitazione**|[Esercitazione](tutorial-develop-table-dotnet.md)|
|**Framework attualmente supportato**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Segnala problema**|[Segnala problema](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Note sulla versione per la serie 2.0.0
la serie 2.0.0 prende la dipendenza da [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), con miglioramenti delle prestazioni e consolidamento dello spazio dei nomi per Cosmos DB endpoint.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* anteprima iniziale di 2.0.0 Table SDK che prende la dipendenza da [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), con miglioramenti delle prestazioni e consolidamento dello spazio dei nomi per Cosmos DB endpoint. L'API pubblica rimane invariata.

## <a name="release-notes-for-100-series"></a>Note sulla versione per la serie 1.0.0
la serie 1.0.0 prende la dipendenza da [Microsoft. Azure. DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="a-name105105"></a><a name="1.0.5"/>1.0.5
* Introdurre la nuova configurazione in TableClientConfiguration per usare l'executor REST per comunicare con Cosmos DB API Tabella

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5-Anteprima
* Correzioni di bug

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Correzioni di bug
* Specificare l'opzione HttpClientTimeout per RestExecutorConfiguration.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-Anteprima
* Correzioni di bug
* Specificare l'opzione HttpClientTimeout per RestExecutorConfiguration.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Correzioni di bug

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versione con disponibilità generale

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* Sono state apportate modifiche al modo in cui è possibile configurare CloudTableClient. Ora accetta un oggetto TableClientConfiguration durante la costruzione. TableClientConfiguration fornisce proprietà diverse per configurare il comportamento del client a seconda che l'endpoint di destinazione sia Cosmos DB API Tabella o API Tabella di archiviazione di Azure.
* Aggiunta del supporto per TableQuery per restituire i risultati ordinati in base a una colonna personalizzata. Questa funzionalità è supportata solo negli endpoint Cosmos DB tabella.
* Aggiunta del supporto per esporre RequestCharges su vari tipi di risultati. Questa funzionalità è supportata solo negli endpoint Cosmos DB tabella.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* È possibile aggiungere il supporto per token di firma di accesso condiviso e le operazioni di TablePermissions, ServiceProperties e ServiceStats sugli endpoint di Tabella di Archiviazione di Azure. 
   > [!NOTE]
   > Alcune funzionalità dei precedenti SDK per Tabella di Archiviazione di Azure non sono ancora supportate, ad esempio la crittografia sul lato client.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* È possibile aggiungere il supporto per le operazioni batch, query e CRUD di base sugli endpoint di Tabella di Archiviazione di Azure. 
   > [!NOTE]
   > Alcune funzionalità dei precedenti SDK per Tabella di Archiviazione di Azure non sono ancora supportate, ad esempio la crittografia sul lato client.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* L'SDK .NET Standard Tabella di Azure Cosmos DB è una libreria .NET multipiattaforma che offre un accesso efficiente al modello di dati Tabella in Cosmos DB. Questa versione iniziale supporta il set completo di Tabella e funzionalità CRUD di entità e query con API simili a quelle dell'[SDK Tabella di Cosmos DB per .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Gli endpoint Tabella di Archiviazione di Azure non sono ancora supportati nella versione di anteprima 0.9.1.

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Questa libreria di .NET Standard multipiattaforma [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) sostituirà la libreria .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>serie 2.0.0
| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>serie 1.0.0
| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 settembre, 2019 |--- |
| [1.0.5-Anteprima](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-Anteprima](#1.0.4-preview) |26 luglio 2019 |--- |
| 1.0.2-anteprima |2 maggio 2019 |--- |
| [1.0.1](#1.0.1) |19 aprile 2019 |--- |
| [1.0.0](#1.0.0) |13 marzo 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 marzo 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 gennaio 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 dicembre 2018 |--- |
| [0.9.1-anteprima](#0.9.1-preview) |18 ottobre 2018 |--- |


## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni sull'API Table di Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB: API Table](table-introduction.md).
