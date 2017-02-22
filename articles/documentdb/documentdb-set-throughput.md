---
title: "Effettuare il provisioning della velocità effettiva per Azure DocumentDB | Microsoft Docs"
description: "Come impostare la velocità effettiva con provisioning per la raccolta di DocumentDB."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 9919f9430626b02635272599e7d1f3f09e0068cd
ms.openlocfilehash: ae83dc01113e72576a35aa57acbac3b71d87e9f8


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Impostare la velocità effettiva per le raccolte di Azure DocumentDB

È possibile impostare la velocità effettiva per le raccolte di DocumentDB nel Portale di Azure o tramite gli SDK del client. 

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Per impostare la velocità effettiva tramite il Portale di Azure

1. In una nuova finestra accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **NoSQL (DocumentDB)** nella barra a sinistra oppure fare clic su **Altri servizi** nella parte inferiore, scorrere fino a **Database**, quindi fare clic su **NoSQL (DocumentDB)**.
3. Selezionare il proprio account DocumentDB.
4. Nella nuova finestra, in **Raccolte**, fare clic su **Scale** (Ridimensiona) come illustrato nella schermata seguente.
5. Nella nuova finestra selezionare la raccolta dall'elenco a discesa, modificare il valore in **Velocità effettiva** quindi fare clic su **Salva**.

    ![Schermata che illustra come modificare la velocità effettiva per una raccolta nel Portale di Azure accedendo al proprio account e facendo clic su Scale (Ridimensiona)](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>Per impostare la velocità effettiva mediante .NET SDK

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul provisioning e sulla diffusione su scala globale di DocumentDB, vedere [Partizionamento e scalabilità con DocumentDB](documentdb-partition-data.md).


<!--HONumber=Feb17_HO2-->


