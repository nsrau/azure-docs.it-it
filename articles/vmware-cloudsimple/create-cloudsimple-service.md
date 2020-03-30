---
title: Soluzione Azure VMware di CloudSimple - Creare il servizio CloudSimpleAzure VMware Solution by CloudSimple - Create CloudSimple service
description: Descrive come creare il servizio CloudSimple nel portale di AzureDescribes how to create the CloudSimple service in the Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024824"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Creare la soluzione Azure VMware dal servizio CloudSimpleCreate the Azure VMware Solution by CloudSimple service

Per iniziare a usare la soluzione Azure VMware di CloudSimple, creare il servizio Soluzione VMware di Azure da CloudSimple nel portale di Azure.To get started with Azure VMware Solution by CloudSimple, create the Azure VMware Solution by CloudSimple service in the Azure portal.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR /28 per la subnet del gateway. È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene utilizzata per i servizi di rete perimetrale e richiede un blocco CIDR /28. Lo spazio di indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.The networks that communicate with CloudSimple include on-premises networks and Azure virtual networks.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale](https://portal.azure.com)di Azure .

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Services**.
    ![Servizio Search CloudSimple](media/create-cloudsimple-service-search.png)
3. Selezionare **CloudSimple Services**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.
    ![Aggiungi servizio CloudSimple](media/create-cloudsimple-service-add.png)
5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.Select the subscription where you want to create the CloudSimple service.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet /28 che non si sovrapponga a subnet locali, subnet di Azure o subnet CloudSimple pianificate. Non è possibile modificare il CIDR dopo la creazione del servizio.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)
9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il provisioning dei [nodi](create-nodes.md)
* Scopri come [creare un cloud privato](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
