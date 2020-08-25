---
title: 'Esercitazione: Ridimensionare un cloud privato'
description: In questa esercitazione si usa il portale di Azure per dimensionare un cloud privato dell'anteprima della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750425"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Esercitazione: Dimensionare un cloud privato dell'anteprima della soluzione Azure VMware

Per sfruttare al meglio l'esperienza del cloud privato dell'anteprima della soluzione Azure VMware, dimensionare i cluster e gli host in base alle esigenze dei carichi di lavoro pianificati. Poiché la soluzione Azure VMware non supporta l'istanza locale di vCenter durante l'anteprima, è necessario usare quella già creata tramite il portale di Azure.

È possibile ridimensionare il numero di cluster e il numero di host in un cloud privato, se necessario per il carico di lavoro dell'applicazione. È necessario risolvere le limitazioni relative a prestazioni e disponibilità per servizi specifici caso per caso all'interno dell'ambiente cloud dell'anteprima della soluzione Azure VMware. I limiti relativi a cluster e host in un cloud privato sono descritti nell'[articolo sui concetti del cloud privato](concepts-private-clouds-clusters.md).

In questa esercitazione si userà il portale di Azure per:

> [!div class="checklist"]
> * Aggiungere un cluster a un cloud privato esistente
> * Aggiungere host a un cluster esistente

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario avere un cloud privato. Se non è ancora stato creato un cloud privato, seguire questa [esercitazione](tutorial-create-private-cloud.md) per crearne uno VMware in Azure e configurare la rete virtuale necessaria.

## <a name="add-a-new-cluster"></a>Aggiungere un nuovo cluster

1. Nella pagina Panoramica di un cloud privato esistente, in **Gestisci**, selezionare **Scale private cloud** (Ridimensiona cloud privato). Selezionare quindi **+ Aggiungi cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

1. Nella pagina **Aggiungi cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Nella pagina Aggiungi cluster usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare Salva." border="true":::

   Verrà avviata la distribuzione del nuovo cluster.

## <a name="scale-a-cluster"></a>Ridimensionare un cluster 

1. Nella pagina Panoramica di un cloud privato esistente selezionare **Scale private cloud** (Ridimensiona cloud privato) e selezionare l'icona della matita per modificare il cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selezionare Scale private cloud in Panoramica" border="true":::

1. Nella pagina **Modifica cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Nella pagina Modifica cluster usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare Salva." border="true":::

   Verrà avviata l'aggiunta di host al cluster.

## <a name="next-steps"></a>Passaggi successivi

Se necessario, [creare un altro cloud privato](tutorial-create-private-cloud.md) della soluzione Azure VMware rispettando gli stessi prerequisiti di rete e i limiti di cluster e host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
