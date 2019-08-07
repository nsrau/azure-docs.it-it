---
title: Creare una soluzione VMware di Azure tramite CloudSimple-Service
description: Viene descritto come creare il servizio CloudSimple nel portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812436"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Creare una soluzione VMware di Azure tramite CloudSimple-Service

Per iniziare a usare la soluzione VMware di Azure con CloudSimple, creare la soluzione VMware di Azure tramite il servizio CloudSimple nel portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR/28 per la subnet del gateway.  È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per i servizi di rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple.  Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.

2. Cercare i **Servizi CloudSimple**.

    ![Cerca servizio CloudSimple](media/create-cloudsimple-service-search.png)

3. Selezionare **Servizi CloudSimple**.

4. Fare clic su **Aggiungi** per creare un nuovo servizio.

    ![Aggiungere il servizio CloudSimple](media/create-cloudsimple-service-add.png)

5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.

6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.

7. Immettere il nome per identificare il servizio.

8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone a nessuna delle subnet esistenti.  Sono incluse le subnet locali, le subnet di Azure o qualsiasi subnet CloudSimple pianificata. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)

9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
