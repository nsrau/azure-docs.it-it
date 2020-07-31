---
title: Paginazione in Azure Cosmos DB
description: Informazioni sui concetti di paging e i token di continuazione
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 978cc67336fe7f6f89970007215da73da0737f08
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433578"
---
# <a name="pagination-in-azure-cosmos-db"></a>Paginazione in Azure Cosmos DB

In Azure Cosmos DB, le query possono avere più pagine di risultati. Questo documento illustra i criteri usati dal motore di query di Azure Cosmos DB per decidere se suddividere i risultati delle query in più pagine. Facoltativamente, è possibile utilizzare i token di continuazione per gestire i risultati delle query che si estendono su più pagine.

## <a name="understanding-query-executions"></a>Informazioni sulle esecuzioni di query

A volte i risultati delle query verranno suddivisi in più pagine. I risultati di ogni pagina vengono generati da un'esecuzione di query separata. Quando non è possibile restituire i risultati della query in un'unica esecuzione, Azure Cosmos DB suddividerà automaticamente i risultati in più pagine.

È possibile specificare il numero massimo di elementi restituiti da una query impostando `MaxItemCount` . `MaxItemCount`Viene specificato per ogni richiesta e garantisce che il motore di query restituisca tale numero di elementi o meno. È possibile impostare `MaxItemCount` su `-1` se non si desidera applicare un limite al numero di risultati per esecuzione di query.

Inoltre, esistono altri motivi per cui il motore di query potrebbe dover suddividere i risultati delle query in più pagine. Sono inclusi:

- Il contenitore è stato limitato e non sono disponibili ur per restituire altri risultati di query
- La risposta dell'esecuzione della query è troppo grande
- Il tempo di esecuzione della query è troppo lungo
- Per il motore di query è stato più efficiente restituire i risultati in esecuzioni aggiuntive

Il numero di elementi restituiti per ogni esecuzione della query sarà sempre minore o uguale a `MaxItemCount` . Tuttavia, è possibile che altri criteri possano avere limitato il numero di risultati che la query potrebbe restituire. Se si esegue la stessa query più volte, il numero di pagine potrebbe non essere costante. Se, ad esempio, una query è limitata, potrebbe essere presente un minor numero di risultati disponibili per pagina, il che significa che la query avrà pagine aggiuntive. In alcuni casi, è anche possibile che la query restituisca una pagina vuota di risultati.

## <a name="handling-multiple-pages-of-results"></a>Gestione di più pagine di risultati

Per garantire risultati accurati delle query, è necessario passare a tutte le pagine. È necessario continuare a eseguire le query fino a quando non sono presenti pagine aggiuntive.

Di seguito sono riportati alcuni esempi per l'elaborazione dei risultati da query con più pagine:

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)

[SDK per Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)

[Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)

[Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Token di continuazione

In .NET SDK e Java SDK è possibile usare facoltativamente i token di continuazione come segnalibro per lo stato della query. Azure Cosmos DB le esecuzioni di query sono senza stato sul lato server e possono essere ripresi in qualsiasi momento utilizzando il token di continuazione. I token di continuazione non sono supportati in SDK Node.js o Python SDK.

Ecco alcuni esempi per l'uso di token di continuazione:

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)

[SDK per Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

Se la query restituisce un token di continuazione, sono presenti ulteriori risultati della query.

Nell'API REST di Azure Cosmos DB è possibile gestire i token di continuazione con l' `x-ms-continuation` intestazione. Come per le query con .NET o Java SDK, se l' `x-ms-continuation` intestazione della risposta non è vuota, significa che la query presenta risultati aggiuntivi.

Fino a quando si usa la stessa versione SDK, i token di continuazione non scadono mai. Facoltativamente, è possibile [limitare le dimensioni di un token di continuazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Indipendentemente dalla quantità di dati o dal numero di partizioni fisiche nel contenitore, le query restituiscono un singolo token di continuazione.

Non è possibile usare i token di continuazione per le query con [Group by](sql-query-group-by.md) o [Distinct](sql-query-keywords.md#distinct) perché queste query richiedono l'archiviazione di una quantità significativa di stato. Per le query con `DISTINCT` , è possibile utilizzare i token di continuazione se si aggiungono `ORDER BY` alla query.

Di seguito è riportato un esempio di una query con `DISTINCT` che può usare un token di continuazione:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola ORDER BY](sql-query-order-by.md)