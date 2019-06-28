---
title: Espandere soluzione VMware Azure da un Cloud privato CloudSimple
description: Viene illustrato come ampliare un Cloud privato CloudSimple esistente per aggiungere capacità in un cluster nuovo o esistente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332730"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Espandere un Cloud privato CloudSimple

CloudSimple offre la possibilità di espandere in modo dinamico un Cloud privato. È possibile iniziare con una configurazione ridotta e infine in base alle esigenze di capacità superiore. Oppure è possibile creare un Cloud privato in base alle esigenze correnti e quindi espandere man mano che aumenta il consumo.

Un Cloud privato è costituita da uno o più cluster vSphere. Ogni cluster può contenere da 3 a 16 nodi.  Quando si espande un Cloud privato, aggiungere nodi al cluster esistente o creare un nuovo cluster. Per espandere un cluster esistente, altri nodi devono essere dello stesso tipo (SKU) come nodi esistenti. Per la creazione di un nuovo cluster, i nodi possono essere di tipo diverso. Per altre informazioni sui limiti del Cloud privato Microsoft, vedere limita nella sezione [Panoramica del cloud privato CloudSimple](cloudsimple-private-cloud.md) articolo.

Viene creato un cloud privato con un valore predefinito **Datacenter** in vCenter.  Ogni Data Center funge da un'entità di livello superiore di gestione.  Per un nuovo cluster, CloudSimple offre la scelta di aggiunta al Data Center esistente o creare un nuovo Data Center.

Come parte della configurazione del nuovo cluster, CloudSimple consente di configurare l'infrastruttura VMware.  Le impostazioni includono le impostazioni di archiviazione per gruppi di dischi rete vSAN, disponibilità elevata VMware e distribuite risorse utilità di pianificazione (DRS).

Un Cloud privato è possibile espandere più volte. Espansione può essere eseguita solo quando rimanere entro i limiti del nodo globale. Ogni volta che si espande un Cloud privato si aggiunge al cluster esistente o crearne uno nuovo.

## <a name="before-you-begin"></a>Prima di iniziare

Nodi devono essere eseguiti prima di espandere del Cloud privato.  Per altre informazioni sul provisioning di nodi, vedere [effettuare il provisioning di nodi per la soluzione VMware da CloudSimple - Azure](create-nodes.md) articolo.  Per la creazione di un nuovo cluster, è necessario disporre di almeno tre nodi disponibili dello stesso SKU.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Espandere un Cloud privato

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire il **risorse** pagina e selezionare il Cloud privato per il quale si desidera espandere.

3. Nella sezione di riepilogo, fare clic su **Espandi**.

    ![Espandere il cloud privato](media/resources-expand-private-cloud.png)

4. Scegliere se espandere il cluster esistente o creare un nuovo cluster vSphere. Quando si apportano modifiche, le informazioni di riepilogo nella pagina viene aggiornate.

    * Per espandere il cluster esistente, fare clic su **espandere il cluster esistente**. Selezionare il cluster che si desidera espandere e immettere il numero di nodi da aggiungere. Ogni cluster può avere un massimo di 16 nodi.
    * Per aggiungere un nuovo cluster, fare clic su **Crea nuovo cluster**. Immettere un nome per il cluster. Selezionare un Data Center esistente o immettere un nome per creare un nuovo Data Center. Scegliere il tipo di nodo. È possibile scegliere un tipo di nodo diverso quando si crea un nuovo cluster vSphere, ma non quando si espande un cluster esistente di vSphere. Selezionare il numero di nodi. Ogni nuovo cluster deve avere almeno tre nodi.

    ![Espandere il cloud privato - aggiunta di nodi](media/resources-expand-private-cloud-add-nodes.png)

5. Fare clic su **Submit** per espandere il cloud privato.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni su [cloud privati](cloudsimple-private-cloud.md)