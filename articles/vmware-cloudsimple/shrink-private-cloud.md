---
title: Compattazione di Azure VMware Solutions (AVS) cloud privato
description: Viene descritto come compattare un cloud privato AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014267"
---
# <a name="shrink-an-avs-private-cloud"></a>Compattare un cloud privato AVS

AVS offre la flessibilità necessaria per compattare dinamicamente un cloud privato AVS. Un cloud privato AVS è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si compatta un cloud privato AVS, si rimuove un nodo dal cluster esistente o si elimina un intero cluster. 

## <a name="before-you-begin"></a>Prima di iniziare

Prima di compattare un cloud privato AVS, è necessario soddisfare le condizioni seguenti. Il cluster di gestione (primo cluster) viene creato al momento della creazione del cloud privato AVS. Non può essere eliminato.

* Un cluster vSphere deve avere tre nodi. Un cluster con solo tre nodi non può essere compattato.
* Lo spazio di archiviazione totale utilizzato non deve superare la capacità totale dopo la compattazione del cluster.
* Controllare se le regole DRS (Distributed Resource Scheduler) impediscono vMotion di una macchina virtuale. Se sono presenti regole, disabilitare o eliminare le regole. Le regole DRS includono le regole di affinità da macchina virtuale a host.


## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Compattazione di un cloud privato AVS

1. [Accedere al portale AVS](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** .

3. Fare clic sul cloud privato AVS che si vuole compattare

4. Nella pagina Riepilogo fare clic su **compatta**.

    ![Compattare il cloud privato AVS](media/shrink-private-cloud.png)

5. Selezionare il cluster che si desidera compattare o eliminare. 

    ![Compattare il cloud privato AVS: selezionare un cluster](media/shrink-private-cloud-select-cluster.png)

6. Selezionare **Rimuovi un nodo** o **Elimina l'intero cluster**. 

7. Verificare la capacità del cluster

8. Fare clic su **Invia** per compattare il cloud privato AVS.

Viene avviata la compattazione del cloud privato AVS. È possibile monitorare lo stato di avanzamento nelle attività. Il processo di compattazione può richiedere alcune ore a seconda dei dati, che devono essere risincronizzati in rete VSAN.

> [!NOTE]
> 1. Se si riduce un cloud privato eliminando l'ultimo o l'unico cluster nel Data Center, il Data Center non verrà eliminato.
> 2. Se si verifica una violazione della regola DRS, il nodo non verrà rimosso dal cluster e la descrizione dell'attività indica che la rimozione di un nodo violerà le regole DRS sul cluster.    


## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati AVS](cloudsimple-private-cloud.md)
