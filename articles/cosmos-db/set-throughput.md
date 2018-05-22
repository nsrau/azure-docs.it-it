---
title: Effettuare il provisioning della velocità effettiva per Azure Cosmos DB | Microsoft Docs
description: Informazioni su come configurare la velocità effettiva di provisioning per i container, le raccolte, i grafici e le tabelle di Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.openlocfilehash: bede91ed3ffc456740a0eb63ed7a15278e99ebe2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Impostare e ottenere la velocità effettiva per i contenitori di Azure Cosmos DB

È possibile impostare la velocità effettiva per i contenitori o un set di contenitori di Azure Cosmos DB nel portale di Azure o usando gli SDK client. 

La tabella seguente elenca la velocità effettiva disponibile per i contenitori:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenitore a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Contenitore partizionato</strong></p></td>
            <td valign="top"><p><strong>Set di contenitori</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>1.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>50.000 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Illimitato</p></td>
            <td valign="top"><p>Illimitato</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Per impostare la velocità effettiva tramite il Portale di Azure

1. In una nuova finestra accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **Azure Cosmos DB** nella barra di sinistra oppure fare clic su **Tutti i servizi** nella parte inferiore, scorrere fino a **Database** e quindi fare clic su **Azure Cosmos DB**.
3. Selezionare l'account Cosmos DB.
4. Nella nuova finestra fare clic su **Esplora dati** dal menu di spostamento.
5. Nella nuova finestra espandere il database e il contenitore e quindi fare clic su **Scale & Settings** (Scalabilità e impostazioni).
6. Nella nuova finestra digitare il nuovo valore per la velocità effettiva nella casella **Velocità effettiva** e quindi fare clic su **Salva**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Per impostare la velocità effettiva usando l'API SQL per .NET

Il frammento di codice seguente recupera la velocità effettiva corrente e la cambia in 500 UR/s. Per il codice di esempio completo, vedere il progetto [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) su GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
// To change the throughput for a set of containers, use the database's selflink instead of the collection's selflink
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Per impostare la velocità effettiva usando l'API SQL per Java

Il frammento di codice seguente recupera la velocità effettiva corrente e la cambia in 500 UR/s. Per un codice di esempio completo, vedere il file [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) su GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Ottenere la velocità effettiva con il comando GetLastRequestStatistics dell'API MongoDB

L'API MongoDB supporta un comando personalizzato, *getLastRequestStatistics*, per il recupero degli addebiti per le richieste per un'operazione specificata.

Ad esempio, in Mongo Shell eseguire l'operazione di cui si vuole verificare l'addebito per le richieste.
```
> db.sample.find()
```

Eseguire quindi il comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Con questa premessa, un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Ottenere la velocità effettiva usando le metriche del portale dell'API MongoDB

Il modo più semplice per ottenere una stima valida degli addebiti per le unità richiesta per il database dell'API MongoDB consiste nell'usare le metriche del [portale di Azure](https://portal.azure.com). I grafici *Numero di richieste* e *Richiedi addebito* consentono di ottenere una stima del numero di unità richiesta utilizzate da ogni operazione e del numero di unità richiesta utilizzate una rispetto all'altra.

![Metriche del portale dell'API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Superamento dei limiti della velocità effettiva riservata nell'API MongoDB
Le applicazioni che superano la velocità effettiva con provisioning per un contenitore o un set di contenitori vengono limitate nella velocità fino a quando il tasso di utilizzo non scende sotto la velocità effettiva con provisioning. In caso di limitazione della velocità, il back-end terminerà preventivamente la richiesta con un codice errore `16500`, ovvero `Too Many Requests`. Per impostazione predefinita, l'API MongoDB ripeterà automaticamente i tentativi fino a 10 volte prima di restituire un codice errore `Too Many Requests`. Se si riceve un numero eccessivo di codici errore `Too Many Requests`, è possibile prendere in considerazione l'aggiunta di una logica di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione oppure l'[aumento della velocità effettiva con provisioning per il contenitore](set-throughput.md).

## <a name="throughput-faq"></a>Domande frequenti sulla velocità effettiva

**È possibile impostare la velocità effettiva a meno di 400 UR/sec?**

Il valore di 400 UR/sec è la velocità effettiva minima disponibile nei contenitori di partizioni singole di Cosmos DB. 1000 UR/sec è il valore minimo per i contenitori partizionati. Le unità richieste sono impostate in intervalli di 100 UR/sec, ma la velocità effettiva non può essere impostata su 100 UR/sec o su qualsiasi valore inferiore a 400 UR/sec. Se si cerca un metodo conveniente per sviluppare e testare Cosmos DB, è possibile usare la versione gratuita dell'[emulatore di Azure Cosmos DB](local-emulator.md), distribuibile in locale senza alcun costo aggiuntivo. 

**Come si configura la velocità effettiva tramite l'API MongoDB?**

Non è disponibile alcuna estensione dell'API MongoDB per la configurazione della velocità effettiva. È consigliabile usare l'API SQL, come mostrato in [Per configurare la velocità effettiva usando l'API SQL per .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul provisioning e sulla diffusione su scala globale di Cosmos DB, vedere [Partizionamento e scalabilità con Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png