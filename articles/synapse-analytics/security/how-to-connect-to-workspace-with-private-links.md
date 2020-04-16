---
title: Connettersi a un'area di lavoro di Azure Synapse usando collegamenti privatiConnect to an Azure Synapse workspace using private links
description: Questo articolo ti insegnerà come connetterti all'area di lavoro di Azure Synapse usando collegamenti privati
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432190"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Connettersi all'area di lavoro di Azure Synapse usando collegamenti privati (anteprima)Connect to your Azure Synapse workspace using private links (preview)

Questo articolo illustra come creare un endpoint privato nell'area di lavoro di Azure Synapse.This article will teach you how to create private endpoint to your Azure Synapse workspace. Per altre informazioni, vedere [collegamenti privati ed endpoint privati.](https://docs.microsoft.com/azure/private-link/)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passaggio 1: Aprire l'area di lavoro Di Azure Synapse nel portale di AzureStep 1: Open your Azure Synapse workspace in Azure portal

Selezionare **Connessione endpoint privata** in **Sicurezza** e quindi selezionare **Endpoint privato**.
![Aprire l'area di lavoro Synapse di Azure nel portale di AzureOpen Azure Synapse workspace in Azure portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Passaggio 2: selezionare l'abbonamento e i dettagli dell'area geograficaStep 2: Select your subscription and region details

Nella scheda **Nozioni di base** della finestra **Crea endpoint privato** scegliere **Sottoscrizione** e Gruppo **di risorse.** Assegnare un **nome** all'endpoint privato che si desidera creare. Selezionare **l'area** in cui si desidera creare l'endpoint privato.

Gli endpoint privati vengono creati in una subnet. La sottoscrizione, il gruppo di risorse e l'area selezionata filtrano le subnet dell'endpoint privato. Al termine, selezionare **Avanti: >delle risorse.**
![Selezionare i dettagli dell'abbonamento e dell'area geografica](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Passaggio 3: Selezionare i dettagli dell'area di lavoro di Azure SynapseStep 3: Select your Azure Synapse workspace details

Selezionare Connetti a una risorsa di Azure nella directory personale nella scheda **Risorsa.** Selezionare la sottoscrizione che contiene l'area di lavoro di Azure Synapse.Select Connect to an **Azure resource in my directory** in the Resource tab. Select the **Subscription** that contains your Azure Synapse workspace. Il **tipo di risorsa** per la creazione di endpoint privati in un'area di lavoro di Azure Synapse è *Microsoft.Synapse/workspaces*.

Selezionare l'area di lavoro Synapse di Azure come **Risorsa**. Ogni area di lavoro synapse di Azure include tre sottorisorse Target per cui è possibile creare un endpoint privato: Sql, SqlOnDemand e Dev.Every Azure Synapse workspace has three **Target sub-resource** that you can create a private endpoint to: Sql, SqlOnDemand, and Dev.

Selezionare **Avanti: Configurazione>** per passare alla parte successiva dell'installazione.
![Selezionare i dettagli dell'abbonamento e dell'area geografica](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Nella scheda **Configurazione** selezionare la **rete virtuale** e la **subnet** in cui deve essere creato l'endpoint privato. È inoltre necessario creare un record DNS che esegue il mapping all'endpoint privato.

Selezionare **Sì** per **Integrazione con zona DNS privata** per integrare l'endpoint privato con una zona DNS privata. Se non si dispone di una zona DNS privata associata alla rete virtuale, viene creata una nuova zona DNS privata. Al termine, selezionare **Revisione e creazione.**

![Selezionare i dettagli dell'abbonamento e dell'area geografica](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Al termine della distribuzione, aprire l'area di lavoro Synapse di Azure nel portale di Azure e selezionare **Connessioni endpoint privati.** Vengono visualizzati il nuovo endpoint privato e il nome della connessione dell'endpoint privato associati all'endpoint privato.

![Selezionare i dettagli dell'abbonamento e dell'area geografica](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete virtuale dell'area di lavoro gestitaLearn](./synapse-workspace-managed-vnet.md) more about Managed workspace VNet

Altre informazioni sugli [endpoint privati gestitiLearn](./synapse-workspace-managed-private-endpoints.md) more about Managed private endpoints

[Creare endpoint privati gestiti nelle origini datiCreate Managed private endpoints to your data sources](./how-to-create-managed-private-endpoints.md)
