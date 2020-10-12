---
title: Connettersi a un'area di lavoro sinapsi usando i collegamenti privati
description: Questo articolo illustra come connettersi all'area di lavoro di Azure Synapse con i collegamenti privati
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ea791e4fc1031c0a5c041119c409f8623ce7aee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91260377"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Connettersi all'area di lavoro di Azure Synapse con i collegamenti privati (anteprima)

Questo articolo illustra come creare un endpoint privato per l'area di lavoro di Azure Synapse. Per altre informazioni fare riferimento ai [collegamenti e agli endpoint privati](https://docs.microsoft.com/azure/private-link/).

## <a name="step-1-register-network-resource-provider"></a>Passaggio 1: Registrare il provider di risorse di rete

Se non è già stato fatto, registrare il provider di risorse di rete. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. Scegliere *Microsoft.Network* dall'elenco dei provider di risorse quando si [esegue la registrazione](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types). Se il provider di risorse di rete è già stato registrato, procedere con il Passaggio 2.


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Passaggio 2: Aprire l'area di lavoro di Azure Synapse nel portale di Azure

Selezionare **Connessione a endpoint privato** in **sicurezza** e quindi selezionare **+ Endpoint privato**.
![Aprire l'area di lavoro di Azure Synapse nel portale di Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Passaggio 3: Selezionare i dettagli per la sottoscrizione e l'area

Nella scheda **Nozioni di base** della finestra **Crea un endpoint privato** scegliere la **Sottoscrizione** e il **Gruppo di risorse**. Assegnare un **Nome** all'endpoint privato che si vuole creare. Selezionare l'**Area** in cui si desidera collocare l'endpoint privato creato.

Gli endpoint privati vengono creati in una subnet. La sottoscrizione, il gruppo di risorse e l'area selezionati filtrano le subnet dell'endpoint privato. Selezionare **Avanti: Risorsa >** al termine.
![Selezionare la sottoscrizione e l'area Dettagli 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Passaggio 4: Selezionare i dettagli dell'area di lavoro di Azure Synapse

Selezionare **Connettersi a una risorsa di Azure nella directory** nella scheda **Risorsa**. Selezionare la **Sottoscrizione** che contiene l'area di lavoro di Azure Synapse. Il **Tipo di risorsa** per la creazione di endpoint privati in un'area di lavoro di Azure Synapse è *Microsoft.Synapse/workspaces*.

Selezionare l'area di lavoro di Azure Synapse come **Risorsa**. Ogni area di lavoro di Azure Synapse ha tre **Risorse secondarie di destinazione** in cui è possibile creare un endpoint privato: SQL, SqlOnDemand e Dev.

Selezionare **Avanti: Configurazione>** per passare alla parte successiva della configurazione.
![Selezionare la sottoscrizione e l'area dettagli 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Nella scheda **Configurazione** selezionare la **Rete virtuale** e la **Subnet** in cui creare l'endpoint privato. È anche necessario creare un record DNS che esegue il mapping nell'endpoint privato.

Selezionare **Sì** per **Integra con la zona DNS privata** per integrare l'endpoint privato con una zona DNS privata. Se non si dispone di una zona DNS privata associata alla Rete virtuale di Microsoft Azure, viene creata una nuova zona DNS privata. Al termine selezionare **Rivedi e crea**.

![Selezionare la sottoscrizione e l'area dettagli 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Al termine della distribuzione, aprire l'area di lavoro di Azure Synapse nel portale di Azure e selezionare **Connessioni a endpoint privati**. Vengono visualizzati il nome del nuovo endpoint privato e della connessione all'endpoint privato associati all'endpoint privato.

![Selezionare la sottoscrizione e l'area dettagli 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)
