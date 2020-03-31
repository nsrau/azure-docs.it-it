---
title: Riduci la soluzione Azure VMware dal cloudSimple Private Cloud
description: Viene descritto come ridurre un cloud privato cloud semplice.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014267"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Ridurre un cloud semplice cloud privato

CloudSimple offre la flessibilità necessaria per ridurre dinamicamente un cloud privato.  Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si riduce un cloud privato, si rimuove un nodo dal cluster esistente o si elimina un intero cluster. 

## <a name="before-you-begin"></a>Prima di iniziare

Le seguenti condizioni devono essere soddisfatte per la riduzione di un cloud privato.  Impossibile eliminare il cluster di gestione (primo cluster) creato al momento della creazione di un cloud privato.

* Un cluster vSphere deve avere tre nodi.  Un cluster con solo tre nodi non può essere ridotto.
* Lo spazio di archiviazione totale utilizzato non deve superare la capacità totale dopo la riduzione del cluster.
* Verificare se le regole DRS (Distributed Resource Scheduler) impedisce vMotion di una macchina virtuale.  Se sono presenti regole, disabilitarle o eliminarle.  Le regole DRS includono la macchina virtuale per ospitare le regole di affinità.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="shrink-a-private-cloud"></a>Ridurre un cloud privato

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **Risorse.**

3. Fare clic sul cloud privato che si desidera ridurre

4. Nella pagina di riepilogo fare clic su **Riduci**.

    ![Riduci cloud privato](media/shrink-private-cloud.png)

5. Selezionare il cluster che si desidera ridurre o eliminare. 

    ![Riduci cloud privato - seleziona cluster](media/shrink-private-cloud-select-cluster.png)

6. Selezionare **Rimuovi un nodo** o Elimina **l'intero cluster**. 

7. Verificare la capacità del cluster

8. Fare clic su **Invia** per ridurre il cloud privato.

Viene avviata la riduzione del cloud privato.  È possibile monitorare lo stato di avanzamento delle attività.  Il processo di compattazione può richiedere alcune ore a seconda dei dati, che devono essere risincronizzati in vSAN.

> [!NOTE]
> 1. Se si riduce un cloud privato eliminando l'ultimo o l'unico cluster nel data center, il data center non verrà eliminato.
> 2. Se si verifica una violazione della regola DRS, il nodo non verrà rimosso dal cluster e la descrizione dell'attività mostra che la rimozione di un nodo violerà le regole DRS nel cluster.    


## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Ulteriori informazioni sui [cloud privati](cloudsimple-private-cloud.md)
