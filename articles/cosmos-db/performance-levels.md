---
title: Livelli di prestazioni di Azure Cosmos DB ritirati
description: Informazioni sui livelli di prestazioni S1, S2 e S3 disponibili in precedenza in Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27e9e8c2cb3201ea4d4522662cd49609a2163997
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865557"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Ritiro dei livelli di prestazioni S1, S2 e S3

> [!IMPORTANT] 
> I livelli di prestazioni S1, S2 e S3 descritti in questo articolo sono in fase di ritiro e non sono più disponibili per i nuovi account Azure Cosmos DB.
>

Questo articolo offre una panoramica dei livelli di prestazioni S1, S2 e S3 e descrive come può essere eseguita la migrazione delle raccolte che usano questi livelli di prestazioni a raccolte a partizione singola. Alla fine della lettura, si avranno le risposte alle domande seguenti:

- [Perché i livelli di prestazioni S1, S2 e S3 sono in fase di ritiro?](#why-retired)
- [Che differenze ci sono tra le raccolte a partizione singola e partizionate e i livelli di prestazioni S1, S2 e S3?](#compare)
- [Cosa bisogna fare per garantire l'accesso ininterrotto ai dati?](#uninterrupted-access)
- [Dopo la migrazione, come cambierà una raccolta esistente?](#collection-change)
- [Dopo la migrazione a raccolte a partizione singola, come cambierà la fatturazione?](#billing-change)
- [Come bisogna comportarsi se sono necessari più di 10 GB di spazio di archiviazione?](#more-storage-needed)
- [È possibile passare tra i livelli di prestazioni S1, S2 e S3 prima della migrazione pianificata?](#change-before)
- [Come è possibile seguire autonomamente la migrazione dai livelli di prestazioni S1, S2 e S3 alle raccolte a partizione singola?](#migrate-diy)
- [Quali sono le conseguenze per i clienti EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Perché i livelli di prestazioni S1, S2 e S3 sono in fase di ritiro?

I livelli di prestazioni S1, S2 e S3 non offrono la stessa flessibilità fornita dall'offerta Standard di Azure Cosmos DB. Con i livelli di prestazioni S1, S2 e S3, la velocità effettiva e la capacità di archiviazione sono preimpostate e non offrono elasticità. Azure Cosmos DB offre ora la possibilità di personalizzare questi valori garantendo una flessibilità decisamente superiore per il ridimensionamento in base alle proprie esigenze.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Che differenze ci sono tra le raccolte a partizione singola e partizionate e i livelli di prestazioni S1, S2 e S3?

Nella tabella seguente vengono confrontate le opzioni di archiviazione e di velocità effettiva disponibili in raccolte a partizione singola, raccolte partizionate e livelli di prestazioni S1, S2 e S3. Di seguito è riportato un esempio per l'area Stati Uniti orientali 2:

|   |Raccolta partizionata|Raccolta a partizione singola|S1|S2|S3|
|---|---|---|---|---|---|
|Velocità effettiva massima|Illimitato|10.000 UR/sec|250 UR/sec|1000 UR/sec|2500 UR/sec|
|Velocità effettiva minima|2500 UR/sec|400 UR/sec|250 UR/sec|1000 UR/sec|2500 UR/sec|
|Spazio di archiviazione massimo|Illimitato|10 GB|10 GB|10 GB|10 GB|
|Prezzo (ogni mese)|Velocità effettiva: $ 6/100 UR/sec<br><br>Spazio di archiviazione: $ 0,25/GB|Velocità effettiva: $ 6/100 UR/sec<br><br>Spazio di archiviazione: $ 0,25/GB|$ 25 USD|$ 50 USD|$ 100 USD|

Per i clienti EA, è consigliabile fare riferimento a [Quali sono le conseguenze per i clienti EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Cosa bisogna fare per garantire l'accesso ininterrotto ai dati?

Se si ha una raccolta S1, S2 o S3, è necessario eseguirne la migrazione a una raccolta a partizione singola a livello di codice [tramite .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Dopo la migrazione, come cambierà una raccolta esistente?

Se si ha una raccolta S1, è possibile eseguirne la migrazione a una raccolta a partizione singola con una velocità effettiva di 400 UR/secondo, cioè la velocità effettiva minima disponibile per le raccolte a partizione singola. Tuttavia, il costo di 400 UR/sec in una raccolta a partizione singola corrisponde approssimativamente a quello di 250 UR/sec pagato in una raccolta S1, per cui i 150 UR/sec aggiuntivi sono praticamente gratuiti.

Se si ha una raccolta S2, è possibile eseguirne la migrazione a una raccolta a partizione singola con una velocità effettiva di 1.000 UR/sec. Non sarà percepita nessuna modifica al livello di velocità effettiva.

Se si ha una raccolta S3, è possibile eseguirne la migrazione a una raccolta a partizione singola con una velocità effettiva di 2.500 UR/sec. Non sarà percepita nessuna modifica al livello di velocità effettiva.

In ognuno di questi casi, dopo la migrazione della raccolta sarà possibile personalizzare il livello di velocità effettiva o aumentarla e ridurla in base alle proprie esigenze, per offrire agli utenti un accesso a bassa latenza. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Dopo la migrazione alle raccolte a partizione singola, come cambierà la fatturazione?

Si supponga di avere a disposizione 10 raccolte S1, ciascuna con 1 GB di spazio di archiviazione, nell'area degli Stati Uniti orientali e di eseguire la migrazione di queste raccolte a 10 raccolte a partizione singola con 400 UR/sec (livello minimo). Se si mantengono le 10 raccolte a partizione singola per un mese intero, la fattura sarà simile a questa:

![Differenza tra i prezzi di 10 raccolte S1 e quelli di 10 raccolte a partizione singola](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Come bisogna comportarsi se sono necessari più di 10 GB di spazio di archiviazione?

Se si ha una raccolta con livello di prestazioni S1, S2 o S3 o una raccolta a partizione singola con spazio di archiviazione disponibile di 10 GB, è possibile usare lo strumento di migrazione dati di Azure Cosmos DB per eseguire la migrazione dei dati a una raccolta partizionata con spazio di archiviazione quasi illimitato. Per informazioni sui vantaggi di una raccolta partizionata, vedere l'articolo relativo a [partizionamento e scalabilità in Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>È possibile passare tra i livelli di prestazioni S1, S2 e S3 prima della migrazione pianificata?

Solo gli account esistenti con prestazioni S1, S2 e S3 possono essere modificati e modificare i livelli di prestazioni a livello di codice [tramite .NET SDK](#migrate-diy). Se si passa da S1, S3 o S3 a una raccolta a partizione singola non è possibile tornare ai livelli di prestazioni S1, S2 o S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Come è possibile eseguire autonomamente la migrazione dai livelli di prestazioni S1, S2, S3 a raccolte a partizione singola?

È possibile eseguire la migrazione dai livelli di prestazioni S1, S2 e S3 a raccolte a partizione singola a livello di codice [tramite .NET SDK](#migrate-diy). È possibile farlo autonomamente prima della migrazione pianificata per poter beneficiare delle opzioni di flessibilità della velocità effettiva, disponibili con le raccolte a partizione singola.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Eseguire la migrazione alle raccolte a partizione singola tramite .NET SDK

Questa sezione illustra solo la modifica del livello di prestazioni di una raccolta tramite l'[API .NET SQL](sql-api-sdk-dotnet.md), ma il processo per gli altri SDK è simile.

Di seguito è riportato un frammento di codice per modificare la velocità effettiva della raccolta a 5000 unità richiesta al secondo:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visitare [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) per visualizzare ulteriori esempi e ulteriori informazioni sui metodi della nostra offerta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Quali sono le conseguenze per i clienti EA?

Il prezzo resterà bloccato per i clienti EA fino alla scadenza del contratto in vigore.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui prezzi e sulla gestione dei dati con Azure Cosmos DB, vedere le risorse seguenti:

1.  [Partizionamento dei dati in Cosmos DB](sql-api-partition-data.md). Informazioni sulla differenza tra contenitore a partizione singola e contenitori partizionati e suggerimenti sull'implementazione di una strategia di partizionamento per eseguire facilmente il ridimensionamento.
2.  [Prezzi di Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Informazioni sui costi del provisioning della velocità effettiva e del consumo di spazio di archiviazione.
3.  [Unità richiesta](request-units.md). Analisi del consumo di velocità effettiva per i diversi tipi di operazione, ad esempio lettura, scrittura, query.
