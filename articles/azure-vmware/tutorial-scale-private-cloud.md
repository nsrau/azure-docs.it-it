---
title: 'Esercitazione: Ridimensionare un cloud privato'
description: In questa esercitazione si usa il portale di Azure per ridimensionare un cloud privato dell'anteprima della soluzione Azure VMware (AVS).
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739726"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Esercitazione: Ridimensionare un cloud privato dell'anteprima della soluzione Azure VMware (AVS)

Per sfruttare al meglio l'esperienza del cloud privato dell'anteprima della soluzione AVS, ridimensionare i cluster e gli host in base alle esigenze dei carichi di lavoro pianificati. Poiché la soluzione AVS non supporta l'istanza locale di vCenter durante l'anteprima, è necessario usare quella già creata tramite il portale di Azure.

È possibile ridimensionare il numero di cluster e il numero di host in un cloud privato, se necessario per il carico di lavoro dell'applicazione. È necessario risolvere le limitazioni relative a prestazioni e disponibilità per servizi specifici caso per caso all'interno dell'ambiente cloud dell'anteprima della soluzione AVS. I limiti relativi a cluster e host in un cloud privato sono descritti nell'[articolo sui concetti del cloud privato](concepts-private-clouds-clusters.md).

In questa esercitazione si userà il portale di Azure per:

> [!div class="checklist"]
> * Aggiungere un cluster a un cloud privato esistente
> * Aggiungere host a un cluster esistente

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario avere un cloud privato. Se non è ancora stato creato un cloud privato, seguire questa [esercitazione](tutorial-create-private-cloud.md) per crearne uno VMware in Azure e configurare la rete virtuale necessaria.

## <a name="add-a-new-cluster"></a>Aggiungere un nuovo cluster

Nella pagina Panoramica di un cloud privato esistente, in **Gestisci**, selezionare **Scale private cloud** (Ridimensiona cloud privato). Selezionare quindi **+ Aggiungi cluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

Nella pagina **Aggiungi cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Configurare un nuovo cluster di cloud privato" border="true":::

Verrà avviata la distribuzione del nuovo cluster.

## <a name="scale-a-cluster"></a>Ridimensionare un cluster 

Nella pagina Panoramica di un cloud privato esistente selezionare **Scale private cloud** (Ridimensiona cloud privato) e selezionare l'icona della matita per modificare il cluster.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selezionare Scale private cloud in Panoramica" border="true":::

Nella pagina **Modifica cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Configurare un nuovo cluster di cloud privato" border="true":::

Verrà avviata l'aggiunta di host al cluster.

## <a name="next-steps"></a>Passaggi successivi

Se necessario, [creare un altro cloud privato](tutorial-create-private-cloud.md) di AVS con gli stessi prerequisiti di rete, i limiti di cluster e host e così via.

<!-- LINKS - external-->

<!-- LINKS - internal -->
