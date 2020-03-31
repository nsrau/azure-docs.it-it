---
title: 'Guida introduttiva: Creare un servizio VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Informazioni su come creare il servizio CloudSimple, acquistare nodi e riservare nodi
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024433"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Guida introduttiva - Creare una soluzione Azure VMware dal servizio CloudSimple

Per iniziare, creare la soluzione Azure VMware tramite CloudSimple nel portale di Azure.To get started, create the Azure VMware Solution by CloudSimple in the Azure portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution di CloudSimple - Panoramica del servizio

Il servizio CloudSimple consente di usare La soluzione Azure VMware di CloudSimple.The CloudSimple service allows you to consume Azure VMware Solution by CloudSimple.  La creazione del servizio consente di eseguire il provisioning dei nodi, riservare nodi e creare cloud privati.  Aggiungere il servizio CloudSimple in ogni area di Azure in cui è disponibile il servizio CloudSimple.You add the CloudSimple service in each Azure region where the CloudSimple service is available.  The service defines the edge network of Azure VMware Solution by CloudSimple.  Questa rete perimetrale viene usata per i servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati.

Per aggiungere il servizio CloudSimple, è necessario creare una subnet del gateway. La subnet del gateway viene utilizzata durante la creazione della rete perimetrale e richiede un blocco CIDR /28. Lo spazio di indirizzi della subnet del gateway deve essere univoco. Non può sovrapporsi a nessuno degli spazi di indirizzi di rete locali o con lo spazio di indirizzi della rete virtuale di Azure.It can't overlap with any of your on-premises network address spaces or Azure virtual network address space.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR /28 per la subnet del gateway.  È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per i servizi di rete perimetrale CloudSimple di Azure E richiede un blocco CIDR /28.The gateway subnet is used for Azure VMware Solution by CloudSimple edge network services and requires a /28 CIDR block. Lo spazio di indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple.  Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.The networks that communicate with CloudSimple include on-premises networks and Azure virtual networks.

Esaminare [I prerequisiti di rete](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Service**.

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

## <a name="provision-nodes"></a>Effettuare il provisioning dei nodi

Per configurare la capacità con pagamento in base al movimento per un ambiente CloudSimple Private Cloud, eseguire innanzitutto il provisioning dei nodi nel portale di Azure.To set up pay-as-you-go capacity for a CloudSimple Private Cloud environment, first provision nodes in the Azure portal.

1. Selezionare **Tutti i servizi**.
2. Cercare **nodi CloudSimple**.

    ![Nodi semplici search Cloud](media/create-cloudsimple-node-search.png)

3. Selezionare **Nodi CloudSimple**.
4. Fare clic su **Aggiungi** per creare nodi.

    ![Aggiunta di nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vuole eseguire il provisioning dei nodi CloudSimple.Select the subscription where you want provision CloudSimple nodes.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse del nodo.
9. Selezionare la posizione dedicata per ospitare le risorse del nodo.
10. Selezionare il [tipo di nodo](cloudsimple-node.md).
11. Selezionare il numero di nodi di cui eseguire il provisioning.
12. Selezionare **Revisione e creazione**.
13. Rivedere le impostazioni. Per modificare le impostazioni, fare clic su **Precedente**.
14. Selezionare **Crea**.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cloud privato e configurare l'ambiente](quickstart-create-private-cloud.md)
* Ulteriori informazioni sul [servizio CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
