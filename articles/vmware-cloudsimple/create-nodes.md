---
title: Eseguire il provisioning di nodi per la soluzione VMware con CloudSimple-Azure
description: Informazioni su come aggiungere nodi a VMWare con la distribuzione di CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 898b07d05abf3bfad644fb590d90c7a90c5a1c0d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883233"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Eseguire il provisioning di nodi per la soluzione VMware con CloudSimple-Azure

Eseguire il provisioning dei nodi nel portale di Azure. Puoi quindi configurare la capacità con pagamento in base al consumo per l'ambiente cloud privato CloudSimple.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Aggiungere un nodo al cloud privato CloudSimple

1. Selezionare **Tutti i servizi**.
2. Cercare i **nodi CloudSimple**.

   ![Cerca nodi CloudSimple](media/create-cloudsimple-node-search.png)

3. Selezionare i **nodi CloudSimple**.
4. Fare clic su **Aggiungi** per creare i nodi.

    ![Aggiungi nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vuole eseguire il provisioning dei nodi CloudSimple.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse del nodo.
9. Selezionare il percorso dedicato per ospitare le risorse del nodo.
10. Selezionare il tipo di nodo. È possibile scegliere l' [opzione CS28 o CS36](cloudsimple-node.md). La seconda opzione include la capacità di calcolo e di memoria massima.
11. Selezionare il numero di nodi di cui eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Esaminare le impostazioni. Per modificare le impostazioni, fare clic su **indietro**.
14. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di un cloud privato](create-private-cloud.md)
