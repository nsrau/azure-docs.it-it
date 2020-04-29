---
title: Connettersi a un'area di lavoro di Azure sinapsi usando i collegamenti privati
description: Questo articolo illustra come connettersi all'area di lavoro di Azure sinapsi usando i collegamenti privati
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432190"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Connettersi all'area di lavoro di Azure sinapsi usando i collegamenti privati (anteprima)

In questo articolo viene illustrato come creare un endpoint privato per l'area di lavoro di Azure sinapsi. Per altre informazioni, vedere [collegamenti privati ed endpoint privati](https://docs.microsoft.com/azure/private-link/) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passaggio 1: aprire l'area di lavoro della sinapsi di Azure in portale di Azure

Selezionare **connessione endpoint privato** in **sicurezza** e quindi selezionare **+ endpoint privato**.
![Aprire l'area di lavoro sinapsi di Azure in portale di Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Passaggio 2: selezionare i dettagli relativi alla sottoscrizione e all'area

Nella scheda **nozioni di base** nella finestra **Crea un endpoint privato** scegliere la **sottoscrizione** e il **gruppo di risorse**. Assegnare un **nome** all'endpoint privato che si vuole creare. Selezionare l' **area** in cui si vuole creare l'endpoint privato.

Gli endpoint privati vengono creati in una subnet. La sottoscrizione, il gruppo di risorse e l'area selezionati filtrano le subnet dell'endpoint privato. Al termine, fare clic su **Next: Resource >** .
![Selezionare i dettagli relativi a sottoscrizione e area](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Passaggio 3: selezionare i dettagli dell'area di lavoro di Azure sinapsi

Selezionare **Connetti a una risorsa di Azure nella directory** nella scheda **risorsa** . Selezionare la **sottoscrizione** che contiene l'area di lavoro di Azure sinapsi. Il **tipo di risorsa** per la creazione di endpoint privati in un'area di lavoro di Azure sinapsi è *Microsoft. sinapsi/Workspaces*.

Selezionare l'area di lavoro di Azure sinapsi come **risorsa**. Ogni area di lavoro di Azure sinapsi presenta tre **risorse secondarie di destinazione** in cui è possibile creare un endpoint privato per: SQL, sqlondemand e dev.

Selezionare **Next: Configuration>** per passare alla parte successiva dell'installazione.
![Selezionare i dettagli relativi a sottoscrizione e area](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Nella scheda **configurazione** selezionare la **rete virtuale** e la **subnet** in cui deve essere creato l'endpoint privato. È anche necessario creare un record DNS che esegue il mapping all'endpoint privato.

Selezionare **Sì** per l' **integrazione con la zona DNS privata** per integrare l'endpoint privato con una zona DNS privata. Se non è presente una zona DNS privata associata alla VNet, viene creata una nuova zona DNS privata. Al termine, selezionare **Verifica + crea** .

![Selezionare i dettagli relativi a sottoscrizione e area](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Al termine della distribuzione, aprire l'area di lavoro sinapsi di Azure in portale di Azure e selezionare **connessioni a endpoint privati**. Vengono visualizzati il nuovo endpoint privato e il nome della connessione all'endpoint privato associato all'endpoint privato.

![Selezionare i dettagli relativi a sottoscrizione e area](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [area di lavoro gestita VNet](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)
