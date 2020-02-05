---
title: Espandi cloud privato di Azure VMware Solutions (AVS)
description: Viene descritto come espandere un cloud privato AVS esistente per aggiungere capacità in un cluster nuovo o esistente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025300"
---
# <a name="expand-an-avs-private-cloud"></a>Espandi un cloud privato AVS

AVS offre la flessibilità necessaria per espandere dinamicamente un cloud privato AVS. È possibile iniziare con una configurazione più piccola e quindi espandersi in quanto è necessaria una capacità superiore. In alternativa, è possibile creare un cloud privato AVS in base alle esigenze correnti e quindi espandersi in base alle crescenze di consumo.

Un cloud privato AVS è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si espande un cloud privato AVS, si aggiungono nodi al cluster esistente o si crea un nuovo cluster. Per espandere un cluster esistente, è necessario che i nodi aggiuntivi siano dello stesso tipo (SKU) dei nodi esistenti. Per la creazione di un nuovo cluster, i nodi possono essere di un tipo diverso. Per altre informazioni sui limiti del cloud privato AVS, vedere la sezione limiti nell'articolo [Panoramica di AVS private cloud](cloudsimple-private-cloud.md) .

Un cloud privato AVS viene creato con un **Data Center** predefinito in vCenter. Ogni Data Center funge da entità di gestione di primo livello. Per un nuovo cluster, AVS consente di scegliere se aggiungere al data center esistente o creare un nuovo Data Center.

Come parte della nuova configurazione del cluster, AVS configura l'infrastruttura VMware. Le impostazioni includono le impostazioni di archiviazione per i gruppi di dischi rete VSAN, la disponibilità elevata VMware e il servizio DRS (Distributed Resource Scheduler).

Un cloud privato AVS può essere espanso più volte. L'espansione può essere eseguita solo quando si rispettano i limiti generali del nodo. Ogni volta che si espande un cloud privato AVS, viene aggiunto al cluster esistente o ne viene creato uno nuovo.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter espandere il cloud privato AVS, è necessario effettuare il provisioning dei nodi. Per altre informazioni sul provisioning dei nodi, vedere l'articolo relativo al provisioning di [nodi per la soluzione VMware da AVS-Azure](create-nodes.md) . Per la creazione di un nuovo cluster, è necessario disporre di almeno tre nodi disponibili dello stesso SKU.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Espandi un cloud privato AVS

1. [Accedere al portale AVS](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** e selezionare il cloud privato AVS per il quale si desidera espandersi.

3. Nella sezione Riepilogo fare clic su **Espandi**.

    ![Espandi il cloud privato AVS](media/resources-expand-private-cloud.png)

4. Scegliere se espandere il cluster esistente o creare un nuovo cluster vSphere. Quando si apportano modifiche, le informazioni di riepilogo sulla pagina vengono aggiornate.

    * Per espandere il cluster esistente, fare clic su **Espandi cluster esistente**. Selezionare il cluster che si vuole espandere e immettere il numero di nodi da aggiungere. Ogni cluster può avere un massimo di 16 nodi.
    * Per aggiungere un nuovo cluster, fare clic su **Crea nuovo cluster**. Immettere un nome per il cluster. Selezionare un data center esistente o immettere un nome per creare un nuovo Data Center. Scegliere il tipo di nodo. È possibile scegliere un tipo di nodo diverso quando si crea un nuovo cluster vSphere, ma non quando si espande un cluster vSphere esistente. Selezionare il numero di nodi. Ogni nuovo cluster deve avere almeno tre nodi.

    ![Espandere AVS private cloud-aggiungere nodi](media/resources-expand-private-cloud-add-nodes.png)

5. Fare clic su **Invia** per espandere il cloud privato AVS.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati AVS](cloudsimple-private-cloud.md)