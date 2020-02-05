---
title: Eseguire il provisioning di nodi per le soluzioni VMware (AVS)-Azure
description: Informazioni su come aggiungere nodi a VMWare con la distribuzione AVS
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024807"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Eseguire il provisioning di nodi per le soluzioni VMware di Azure (AVS)

Eseguire il provisioning dei nodi nel portale di Azure. Puoi quindi configurare la capacità con pagamento in base al consumo per l'ambiente di cloud privato AVS.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Aggiungere un nodo al cloud privato AVS

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

* [Creare un cloud privato AVS](create-private-cloud.md)
