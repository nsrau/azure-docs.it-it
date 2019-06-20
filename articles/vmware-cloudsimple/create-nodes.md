---
title: Effettuare il provisioning di nodi per la soluzione VMware da CloudSimple - Azure
description: Informazioni su come aggiungere nodi a di VMWare con CloudSimple distribuzione
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165248"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Effettuare il provisioning di nodi per la soluzione VMware da CloudSimple - Azure

Il provisioning dei nodi nel portale di Azure. È possibile configurare pay-capacità di passare la capacità per l'ambiente di cloud privato CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Aggiungere un nodo con provisioning per il cloud privato CloudSimple

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple nodi**.

   ![Ricerca CloudSimple nodi](media/create-cloudsimple-node-search.png)

3. Selezionare **CloudSimple nodi**.
4. Fare clic su **Add** per creare nodi.

    ![Aggiungere nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vuole effettuare il provisioning di nodi CloudSimple.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse di nodo.
9. Selezionare la posizione dedicata per ospitare le risorse di nodo.
10. Selezionare il tipo di nodo. È possibile scegliere il [opzione CS28 o CS36](cloudsimple-node.md). L'opzione di quest'ultima include la capacità di calcolo e memoria massima.
11. Selezionare il numero di nodi per eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Rivedere le impostazioni. Per modificare le impostazioni, fare clic su **Previous**.
14. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un Cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
