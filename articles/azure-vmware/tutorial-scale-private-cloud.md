---
title: 'Esercitazione: Dimensionare un cloud privato'
description: In questa esercitazione si usa il portale di Azure per dimensionare un cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791240"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Dimensionare un cloud privato della soluzione Azure VMware

Per sfruttare al meglio l'esperienza del cloud privato della soluzione Azure VMware, dimensionare i cluster e gli host in base alle esigenze dei carichi di lavoro pianificati. È possibile ridimensionare i cluster e gli host in un cloud privato, se necessario per il carico di lavoro dell'applicazione. Le limitazioni relative alle prestazioni e alla disponibilità di servizi specifici dovranno essere risolte caso per caso. I limiti relativi a cluster e host sono descritti nell'[articolo sui concetti del cloud privato](concepts-private-clouds-clusters.md).

In questa esercitazione verrà usato il portale di Azure per:

> [!div class="checklist"]
> * Aggiungere un cluster a un cloud privato esistente
> * Aggiungere host a un cluster esistente

## <a name="prerequisites"></a>Prerequisiti

Un cloud privato per completare l'esercitazione. Se non è stato creato un cloud privato, usare l'esercitazione [Creare un cloud privato](tutorial-create-private-cloud.md) per crearne uno. Configurare la rete per il cloud privato VMware in Azure per impostare la rete virtuale richiesta.

## <a name="add-a-new-cluster"></a>Aggiungere un nuovo cluster

1. Nella pagina Panoramica di un cloud privato esistente, in **Gestisci** , selezionare **Scale private cloud** (Ridimensiona cloud privato). Selezionare quindi **+ Aggiungi cluster** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

1. Nella pagina **Aggiungi cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

   Verrà avviata la distribuzione del nuovo cluster.

## <a name="scale-a-cluster"></a>Ridimensionare un cluster 

1. Nella pagina Panoramica di un cloud privato esistente selezionare **Scale private cloud** (Ridimensiona cloud privato) e selezionare l'icona della matita per modificare il cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selezionare Aggiungi cluster" border="true":::

1. Nella pagina **Modifica cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

   Verrà avviata l'aggiunta di host al cluster.

## <a name="next-steps"></a>Passaggi successivi

Se necessario, [creare un altro cloud privato](tutorial-create-private-cloud.md) della soluzione Azure VMware rispettando gli stessi prerequisiti di rete e i limiti di cluster e host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
