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
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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
            <td valign="top"><p>2.500 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Senza limiti</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Per impostare la velocità effettiva tramite il Portale di Azure

1. In una nuova finestra accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **Azure Cosmos DB** nella barra a sinistra oppure fare clic su **Altri servizi** nella parte inferiore, scorrere fino a **Database**, quindi fare clic su **Azure Cosmos DB**.
3. Selezionare l'account Cosmos DB.
4. Nella nuova finestra fare clic su **Esplora dati (anteprima)** dal menu di spostamento.
5. Nella nuova finestra espandere il database e il contenitore e quindi fare clic su **Scale & Settings** (Scalabilità e impostazioni).
6. Nella nuova finestra digitare il nuovo valore per la velocità effettiva nella casella **Velocità effettiva** e quindi fare clic su **Salva**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Per configurare la velocità effettiva usando l'API DocumentDB per .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Domande frequenti sulla velocità effettiva

**È possibile impostare la velocità effettiva a meno di 400 UR/sec?**

Il valore di 400 UR/sec è la velocità effettiva minima disponibile nelle raccolte di partizioni singole di Cosmos DB. 2500 UR/sec è il valore minimo per le raccolte partizionate. Le unità richieste sono impostate in intervalli di 100 UR/sec, ma la velocità effettiva non può essere impostata su 100 UR/sec o su qualsiasi valore inferiore a 400 UR/sec. Se si cerca un metodo conveniente per sviluppare e testare Cosmos DB, è possibile usare la versione gratuita dell'[emulatore di Azure Cosmos DB](local-emulator.md), distribuibile in locale senza alcun costo aggiuntivo. 

**Come configurare la velocità effettiva tramite l'API MongoDB**

Non è disponibile alcuna estensione dell'API MongoDB per la configurazione della velocità effettiva. È consigliabile usare l'API DocumentDB, come mostrato in [Per configurare la velocità effettiva usando l'API DocumentDB per .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul provisioning e sulla diffusione su scala globale di Cosmos DB, vedere [Partizionamento e scalabilità con Cosmos DB](partition-data.md).
