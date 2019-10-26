---
title: 'Soluzione VMware di Azure di CloudSimple: creare un servizio CloudSimple'
description: Viene descritto come creare il servizio CloudSimple nel portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893938"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Creare la soluzione VMware di Azure tramite il servizio CloudSimple

Per iniziare a usare la soluzione VMware di Azure con CloudSimple, creare la soluzione VMware di Azure tramite il servizio CloudSimple nel portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR/28 per la subnet del gateway. È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per i servizi di rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
    ![ricerca servizio CloudSimple](media/create-cloudsimple-service-search.png)
3. Selezionare **Servizi CloudSimple**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.
    ![aggiungere il servizio CloudSimple](media/create-cloudsimple-service-add.png)
5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet CloudSimple pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)
9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il [provisioning di nodi](create-nodes.md)
* Informazioni su come [creare un cloud privato](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
