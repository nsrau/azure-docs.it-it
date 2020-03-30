---
title: Eliminare nodi per la soluzione VMware da CloudSimple - AzureDelete nodes for VMware Solution by CloudSimple - Azure
description: Informazioni su come eliminare nodi dalla distribuzione DI VMWare con CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024739"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Eliminare nodi dalla soluzione Azure VMware di CloudSimpleDelete nodes from Azure VMware Solution by CloudSimple

I nodi CloudSimple vengono misurati una volta creati.  I nodi devono essere eliminati per interrompere la misurazione dei nodi.  Eliminare i nodi che non vengono utilizzati dal portale di Azure.You delete the nodes that are not used from Azure portal.

## <a name="before-you-begin"></a>Prima di iniziare

Un nodo può essere eliminato solo nelle seguenti condizioni:

* Un cloud privato creato con i nodi viene eliminato.  Per eliminare un cloud privato, vedere Eliminare una [soluzione Azure VMware dal](delete-private-cloud.md)cloud privato semplice .
* Il nodo è stato rimosso dal cloud privato riducendo il cloud privato.  Per ridurre la riduzione di un cloud privato, vedere [Ridurre la soluzione Azure VMware dal cloudSimple Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="delete-cloudsimple-node"></a>Elimina nodo CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare **nodi CloudSimple**.

   ![Nodi semplici search Cloud](media/create-cloudsimple-node-search.png)

3. Selezionare **Nodi CloudSimple**.

4. Selezionare i nodi che non appartengono a un cloud privato da eliminare.  **Private CLOUD NAME** mostra il nome del cloud privato a cui appartiene un nodo.  Se un nodo non viene utilizzato da un cloud privato, il valore sarà vuoto. 

    ![Seleziona nodi CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Solo i nodi che non fanno parte del cloud privato possono essere eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sul [cloud privato](cloudsimple-private-cloud.md)
