---
title: Eseguire il provisioning dei nodi per la soluzione VMware tramite CloudSimple - AzureProvision nodes for VMware Solution by CloudSimple - Azure
description: Informazioni su come aggiungere nodi alla distribuzione di VMWare con CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024807"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Eseguire il provisioning dei nodi per la soluzione VMware di Azure da CloudSimpleProvision nodes for Azure VMware Solution by CloudSimple

Effettuare il provisioning dei nodi nel portale di Azure.Provision nodes in the Azure portal. È quindi possibile configurare la capacità con pagamento in base al numero di utenti per l'ambiente cloud privato CloudSimple.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Aggiungere un nodo al cloud privato CloudSimpleAdd a node to your CloudSimple private cloud

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

* [Crea cloud privato](create-private-cloud.md)
