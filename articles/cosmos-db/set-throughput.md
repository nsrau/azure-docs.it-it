---
title: "Effettuare il provisioning della velocità effettiva per Azure Cosmos DB | Microsoft Docs"
description: "Informazioni su come configurare la velocità effettiva di provisioning per i container, le raccolte, i grafici e le tabelle di Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: cf6eadbae328b1551da861fb5a11930ee830d415
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Impostare la velocità effettiva per i contenitori di Azure Cosmos DB

È possibile impostare la velocità effettiva per i contenitori di Azure Cosmos DB nel portale di Azure oppure usando gli SDK client. 

La tabella seguente elenca la velocità effettiva disponibile per i contenitori:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenitore a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Contenitore partizionato</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>unità di richiesta di 1000 al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Illimitato</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Per impostare la velocità effettiva tramite il Portale di Azure

1. In una nuova finestra accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **Azure Cosmos DB** nella barra a sinistra oppure fare clic su **Altri servizi** nella parte inferiore, scorrere fino a **Database**, quindi fare clic su **Azure Cosmos DB**.
3. Selezionare l'account Cosmos DB.
4. Nella nuova finestra, fare clic su **Esplora dati** nel menu di navigazione.
5. Nella nuova finestra espandere il database e il contenitore e quindi fare clic su **Scale & Settings** (Scalabilità e impostazioni).
6. Nella nuova finestra digitare il nuovo valore per la velocità effettiva nella casella **Velocità effettiva** e quindi fare clic su **Salva**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Per impostare la velocità effettiva tramite l'API di SQL per .NET

```C#
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Per impostare la velocità effettiva tramite l'API di SQL per Java

Questo frammento di codice è tratto dal file OfferCrudSamples.java nel [java di azure documentdb](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) repository. 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 10300;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>Domande frequenti sulla velocità effettiva

**È possibile impostare la velocità effettiva a meno di 400 UR/sec?**

400 UR/sec è la velocità effettiva minima disponibile per i contenitori di singola partizione DB Cosmos (1000 UR/sec è il valore minimo per i contenitori partizionati). Le unità richieste sono impostate in intervalli di 100 UR/sec, ma la velocità effettiva non può essere impostata su 100 UR/sec o su qualsiasi valore inferiore a 400 UR/sec. Se si cerca un metodo conveniente per sviluppare e testare Cosmos DB, è possibile usare la versione gratuita dell'[emulatore di Azure Cosmos DB](local-emulator.md), distribuibile in locale senza alcun costo aggiuntivo. 

**Come configurare la velocità effettiva tramite l'API MongoDB**

Non è disponibile alcuna estensione dell'API MongoDB per la configurazione della velocità effettiva. Consiglia di utilizzare l'API di SQL, come illustrato nella [per impostare la velocità effettiva tramite l'API di SQL per .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul provisioning e sulla diffusione su scala globale di Cosmos DB, vedere [Partizionamento e scalabilità con Cosmos DB](partition-data.md).
