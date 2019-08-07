---
title: Espandi la soluzione VMware di Azure in base al cloud privato CloudSimple
description: Viene descritto come espandere un cloud privato CloudSimple esistente per aggiungere capacità in un cluster nuovo o esistente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816168"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Espandi un cloud privato CloudSimple

CloudSimple offre la flessibilità necessaria per espandere dinamicamente un cloud privato. È possibile iniziare con una configurazione più piccola e quindi espandersi in quanto è necessaria una capacità superiore. In alternativa, è possibile creare un cloud privato in base alle esigenze correnti e quindi espandersi Man mano che aumenta l'utilizzo.

Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi.  Quando si espande un cloud privato, si aggiungono nodi al cluster esistente o si crea un nuovo cluster. Per espandere un cluster esistente, è necessario che i nodi aggiuntivi siano dello stesso tipo (SKU) dei nodi esistenti. Per la creazione di un nuovo cluster, i nodi possono essere di un tipo diverso. Per altre informazioni sui limiti del cloud privato, vedere la sezione limiti nell'articolo [Panoramica di cloud privato CloudSimple](cloudsimple-private-cloud.md) .

Un cloud privato viene creato con un **Data Center** predefinito in vCenter.  Ogni Data Center funge da entità di gestione di primo livello.  Per un nuovo cluster, CloudSimple offre la possibilità di aggiungere al data center esistente o di creare un nuovo Data Center.

Come parte della nuova configurazione del cluster, CloudSimple configura l'infrastruttura VMware.  Le impostazioni includono le impostazioni di archiviazione per i gruppi di dischi rete VSAN, la disponibilità elevata VMware e il servizio DRS (Distributed Resource Scheduler).

Un cloud privato può essere espanso più volte. L'espansione può essere eseguita solo quando si rispettano i limiti generali del nodo. Ogni volta che si espande un cloud privato, è necessario aggiungerlo al cluster esistente o crearne uno nuovo.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter espandere il cloud privato, è necessario eseguire il provisioning dei nodi.  Per altre informazioni sul provisioning dei nodi, vedere l'articolo relativo al provisioning di [nodi per la soluzione VMware con CloudSimple-Azure](create-nodes.md) .  Per la creazione di un nuovo cluster, è necessario disporre di almeno tre nodi disponibili dello stesso SKU.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Espandi un cloud privato

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** e selezionare il cloud privato per il quale si desidera espandersi.

3. Nella sezione Riepilogo fare clic su **Espandi**.

    ![Espandi cloud privato](media/resources-expand-private-cloud.png)

4. Scegliere se espandere il cluster esistente o creare un nuovo cluster vSphere. Quando si apportano modifiche, le informazioni di riepilogo sulla pagina vengono aggiornate.

    * Per espandere il cluster esistente, fare clic su **Espandi cluster esistente**. Selezionare il cluster che si vuole espandere e immettere il numero di nodi da aggiungere. Ogni cluster può avere un massimo di 16 nodi.
    * Per aggiungere un nuovo cluster, fare clic su **Crea nuovo cluster**. Immettere un nome per il cluster. Selezionare un data center esistente o immettere un nome per creare un nuovo Data Center. Scegliere il tipo di nodo. È possibile scegliere un tipo di nodo diverso quando si crea un nuovo cluster vSphere, ma non quando si espande un cluster vSphere esistente. Selezionare il numero di nodi. Ogni nuovo cluster deve avere almeno tre nodi.

    ![Espandi cloud privato-Aggiungi nodi](media/resources-expand-private-cloud-add-nodes.png)

5. Fare clic su **Invia** per espandere il cloud privato.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati](cloudsimple-private-cloud.md)