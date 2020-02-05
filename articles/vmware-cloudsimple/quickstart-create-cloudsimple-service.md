---
title: Guida introduttiva alle soluzioni VMware di Azure (AVS)-Crea servizio
description: Informazioni su come creare i nodi AVS Service, Purchase Nodes e Reserve
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024433"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Guida introduttiva-creare un servizio Azure VMware Solutions (AVS)

Per iniziare, creare le soluzioni VMware di Azure (AVS) nell'portale di Azure.

## <a name="vmware-solutions-avs---service-overview"></a>Soluzioni VMware (AVS)-Panoramica servizio

Il servizio AVS consente di usare la soluzione VMware di Azure con AVS. La creazione del servizio consente di effettuare il provisioning di nodi, riservare nodi e creare cloud privati AVS. Si aggiunge il servizio AVS in ogni area di Azure in cui è disponibile il servizio AVS. Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite AVS. Questa rete perimetrale viene usata per i servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati AVS.

Per aggiungere il servizio AVS, è necessario creare una subnet del gateway. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non può sovrapporsi ad alcuno degli spazi di indirizzi della rete locale o dello spazio di indirizzi della rete virtuale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR/28 per la subnet del gateway. È necessaria una subnet del gateway per ogni servizio AVS ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per la soluzione VMware di Azure da AVS Edge Network Services e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente AVS. Le reti che comunicano con AVS includono le reti locali e le reti virtuali di Azure.

Verificare i [prerequisiti di rete](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare il **servizio AVS**.

    ![Cerca servizio AVS](media/create-cloudsimple-service-search.png)

3. Selezionare **AVS Services**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.

    ![Aggiungi servizio AVS](media/create-cloudsimple-service-add.png)

5. Selezionare la sottoscrizione in cui si vuole creare il servizio AVS.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet AVS pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio AVS](media/create-cloudsimple-service.png)

9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="provision-nodes"></a>Effettuare il provisioning dei nodi

Per configurare la capacità con pagamento in base al consumo per un ambiente cloud privato AVS, prima effettuare il provisioning dei nodi nel portale di Azure.

1. Selezionare **Tutti i servizi**.
2. Cercare i **nodi AVS**.

    ![Cerca nodi AVS](media/create-cloudsimple-node-search.png)

3. Selezionare **AVS nodes**.
4. Fare clic su **Aggiungi** per creare i nodi.

    ![Aggiungi nodi AVS](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vuole eseguire il provisioning di nodi AVS.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse del nodo.
9. Selezionare il percorso dedicato per ospitare le risorse del nodo.
10. Selezionare il [tipo di nodo](cloudsimple-node.md).
11. Selezionare il numero di nodi di cui eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Esaminare le impostazioni. Per modificare le impostazioni, fare clic su **indietro**.
14. Selezionare **Create** (Crea).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cloud privato AVS e configurare l'ambiente](quickstart-create-private-cloud.md)
* Altre informazioni sul [servizio AVS](https://docs.azure.cloudsimple.com/cloudsimple-service)
