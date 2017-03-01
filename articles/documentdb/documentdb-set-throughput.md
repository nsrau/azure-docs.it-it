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
ms.date: 02/15/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: abdf0af8a85db19c68a0d74c0477d798c0fd03fc
ms.openlocfilehash: 8ff2fc6106438e35b93112a6dc97814ba79b06fc
ms.lasthandoff: 02/22/2017


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Impostare la velocità effettiva per le raccolte di Azure DocumentDB

È possibile impostare la velocità effettiva per le raccolte di DocumentDB nel Portale di Azure o tramite gli SDK del client. 

La tabella seguente elenca la velocità effettiva disponibile per le raccolte:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Raccolta a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Raccolta partizionata</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>2.500 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Illimitato</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> Per impostare le raccolte partizionate su un valore di velocità effettiva compreso tra 2.500 e 10.000 UR/s, è necessario utilizzare temporaneamente il portale di Azure. Questa funzionalità non è ancora disponibile negli SDK.

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Per impostare la velocità effettiva tramite il Portale di Azure

1. In una nuova finestra accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **NoSQL (DocumentDB)** nella barra a sinistra oppure fare clic su **Altri servizi** nella parte inferiore, scorrere fino a **Database**, quindi fare clic su **NoSQL (DocumentDB)**.
3. Selezionare il proprio account DocumentDB.
4. Nella nuova finestra, in **Raccolte**, fare clic su **Scale** (Ridimensiona) come illustrato nella schermata seguente.
5. Nella nuova finestra selezionare la raccolta dall'elenco a discesa, modificare il valore in **Velocità effettiva** quindi fare clic su **Salva**.

    ![Schermata che illustra come modificare la velocità effettiva per una raccolta nel Portale di Azure accedendo al proprio account e facendo clic su Scale (Ridimensiona)](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

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

## <a name="throughput-faq"></a>Domande frequenti sulla velocità effettiva

**È possibile impostare la velocità effettiva a meno di 400 UR/sec?**

Il valore di&400; UR/sec è la velocità effettiva minima disponibile nelle raccolte di partizioni singole DocumentDB (2500 UR/sec è il valore minimo per le raccolte partizionate). Le unità richieste sono impostate in intervalli di 100 UR/sec, ma la velocità effettiva non può essere impostata su 100 UR/sec o su qualsiasi valore inferiore a 400 UR/sec. Se si cerca un metodo conveniente per sviluppare e testare DocumentDB, è possibile usare la versione gratuita di [Emulatore di DocumentDB](documentdb-nosql-local-emulator.md), distribuibile in locale senza alcun costo aggiuntivo. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul provisioning e sulla diffusione su scala globale di DocumentDB, vedere [Partizionamento e scalabilità con DocumentDB](documentdb-partition-data.md).

