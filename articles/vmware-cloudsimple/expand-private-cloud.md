---
title: Espandi la soluzione Azure VMware dal cloudSimple Private Cloud
description: Descrive come espandere un cloud privato CloudSimple esistente per aggiungere capacità in un cluster nuovo o esistente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025300"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Espandere un cloud privato semplice

CloudSimple offre la flessibilità necessaria per espandere dinamicamente un cloud privato. È possibile iniziare con una configurazione più piccola e quindi espandersi in base alla necessità di una capacità superiore. In alternativa, è possibile creare un cloud privato in base alle esigenze attuali e quindi espandersi man mano che il consumo aumenta.

Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi.  Quando si espande un cloud privato, si aggiungono nodi al cluster esistente o si crea un nuovo cluster. Per espandere un cluster esistente, i nodi aggiuntivi devono essere dello stesso tipo (SKU) dei nodi esistenti. Per la creazione di un nuovo cluster, i nodi possono essere di tipo diverso. Per altre informazioni sui limiti del cloud privato, vedere la sezione relativa ai limiti nell'articolo Panoramica del cloud privato CloudSimple.For more information on Private Cloud limits, see limits section in [CloudSimple private cloud overview](cloudsimple-private-cloud.md) article.

Un cloud privato viene creato con un **data center** predefinito in vCenter.  Ogni data center funge da entità di gestione di primo livello.  Per un nuovo cluster, CloudSimple offre la scelta di aggiungere al datacenter esistente o creare un nuovo datacenter.

Come parte della nuova configurazione del cluster, CloudSimple configura l'infrastruttura VMware.  Le impostazioni includono le impostazioni di archiviazione per i gruppi di dischi vSAN, VMware High Availability e Distributed Resource Scheduler (DRS).

Un cloud privato può essere espanso più volte. L'espansione può essere eseguita solo quando si rimane entro i limiti complessivi del nodo. Ogni volta che si espande un cloud privato aggiunto al cluster esistente o ne si crea uno nuovo.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario eseguire il provisioning dei nodi prima di poter espandere il cloud privato.  Per altre informazioni sul provisioning dei nodi, vedere l'articolo [Eseguire il provisioning di nodi per la soluzione VMware di CloudSimple - Azure.For](create-nodes.md) more information on provisioning nodes, see Provision nodes for VMware Solution by CloudSimple - Azure article.  Per creare un nuovo cluster, è necessario disporre di almeno tre nodi disponibili dello stesso SKU.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="expand-a-private-cloud"></a>Espandere un cloud privato

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **Risorse** e selezionare il cloud privato di cui si desidera espandere.

3. Nella sezione di riepilogo fare clic su **Espandi**.

    ![Espandere il cloud privato](media/resources-expand-private-cloud.png)

4. Scegliere se espandere il cluster esistente o creare un nuovo cluster vSphere.Choose whether to expand your existing cluster or create a new vSphere cluster. Quando si apportano modifiche, le informazioni di riepilogo nella pagina vengono aggiornate.

    * Per espandere il cluster esistente, fare clic su **Espandi cluster esistente**. Selezionare il cluster che si desidera espandere e immettere il numero di nodi da aggiungere. Ogni cluster può avere un massimo di 16 nodi.
    * Per aggiungere un nuovo cluster, fare clic su **Crea nuovo cluster**. Immettere un nome per il cluster. Selezionare un data center esistente o immettere un nome per creare un nuovo data center. Scegliere il tipo di nodo. È possibile scegliere un tipo di nodo diverso durante la creazione di un nuovo cluster vSphere, ma non quando si espande un cluster vSphere esistente. Selezionare il numero di nodi. Ogni nuovo cluster deve avere almeno tre nodi.

    ![Espandere il cloud privato - aggiungere nodiExpand private cloud - add nodes](media/resources-expand-private-cloud-add-nodes.png)

5. Fare clic su **Invia** per espandere il cloud privato.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Ulteriori informazioni sui [cloud privati](cloudsimple-private-cloud.md)