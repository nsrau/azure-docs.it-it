---
title: Azure VMware Solutions (AVS)-creare il servizio AVS
description: Viene descritto come creare il servizio AVS nel portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024824"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Creare il servizio Azure VMware Solutions (AVS)

Per iniziare a usare le soluzioni VMware di Azure (AVS), creare il servizio Azure VMware Solutions (AVS) nell'portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR/28 per la subnet del gateway. È necessaria una subnet del gateway per ogni servizio AVS ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per i servizi di rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente AVS. Le reti che comunicano con AVS includono le reti locali e le reti virtuali di Azure.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare **AVS Services**.
    ![Cerca servizio AVS](media/create-cloudsimple-service-search.png)
3. Selezionare **AVS Services**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.
    ![aggiungere il servizio AVS](media/create-cloudsimple-service-add.png)
5. Selezionare la sottoscrizione in cui si vuole creare il servizio AVS.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet AVS pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio AVS](media/create-cloudsimple-service.png)
9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il [provisioning di nodi](create-nodes.md)
* Informazioni su come [creare un cloud privato AVS](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato AVS](quickstart-create-private-cloud.md)
