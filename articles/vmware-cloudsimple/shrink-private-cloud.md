---
title: Compattare soluzione VMware Azure da un Cloud privato CloudSimple
description: Viene descritto come compattare un Cloud privato CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544517"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Compattazione di un Cloud privato CloudSimple

CloudSimple offre la possibilità di ridurre in modo dinamico un Cloud privato.  Un Cloud privato è costituita da uno o più cluster vSphere. Ogni cluster può contenere da 3 a 16 nodi. Durante la compattazione di un Cloud privato, per rimuovere un nodo dal cluster esistente o eliminare un intero cluster. 

## <a name="before-you-begin"></a>Prima di iniziare

Le condizioni seguenti devono essere soddisfatti per la compattazione di un Cloud privato.  Gestione del cluster (primo cluster) creato quando è stato creato un Cloud privato non può essere eliminato.

* Un cluster vSphere deve avere tre nodi.  Impossibile compattare un cluster con nodi solo tre.
* Spazio di archiviazione totale usato non deve superare la capacità totale dopo la compattazione del cluster. 

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Compattazione di un Cloud privato 

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire il **risorse** pagina.

3. Fare clic sul Cloud privato che si desidera ridurre

4. Nella pagina di riepilogo, fare clic su **compattare**.

    ![Compattazione del cloud privato](media/shrink-private-cloud.png)

5. Selezionare il cluster che si desidera ridurre o eliminare. 

    ![Cloud privato di compattazione - cluster select](media/shrink-private-cloud-select-cluster.png)

6. Selezionare **rimuovere un nodo** oppure **eliminare l'intero cluster**. 

7. Verificare la capacità del cluster

8. Fare clic su **Submit** compattazione del Cloud privato.

Verrà avviata la compattazione del Cloud privato.  È possibile monitorare lo stato di avanzamento nelle attività.  Il processo di compattazione può richiedere alcune ore a seconda dei dati, che deve essere resynced su rete vSAN.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni su [cloud privati](cloudsimple-private-cloud.md)