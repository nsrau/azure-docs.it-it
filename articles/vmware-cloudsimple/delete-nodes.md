---
title: Eliminare i nodi per le soluzioni VMware (AVS)-Azure
description: Informazioni su come eliminare i nodi da VMWare con la distribuzione AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024739"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Eliminare i nodi dalla soluzione VMware di Azure con AVS

I nodi AVS vengono conteggiati una volta creati. Per arrestare la misurazione dei nodi, è necessario eliminare i nodi. Si eliminano i nodi non utilizzati da portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Un nodo può essere eliminato solo nelle condizioni seguenti:

* Viene eliminato un cloud privato AVS creato con i nodi. Per eliminare un cloud privato AVS, vedere [eliminare una soluzione VMware di Azure da AVS private cloud](delete-private-cloud.md).
* Il nodo è stato rimosso dal cloud privato AVS compattando il cloud privato AVS. Per compattare un cloud privato AVS, vedere la pagina relativa alla [compattazione della soluzione VMware di Azure da AVS private cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Elimina nodo AVS

1. Selezionare **Tutti i servizi**.

2. Cercare i **nodi AVS**.

   ![Cerca nodi AVS](media/create-cloudsimple-node-search.png)

3. Selezionare **AVS nodes**.

4. Selezionare i nodi che non appartengono a un cloud privato AVS da eliminare. La colonna **nome del cloud privato AVS** Mostra il nome del cloud privato AVS a cui appartiene un nodo. Se un nodo non è usato da un cloud privato AVS, il valore sarà vuoto. 

    ![Selezionare i nodi AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Solo i nodi che non fanno parte del cloud privato AVS possono essere eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [AVS private cloud](cloudsimple-private-cloud.md)
