---
title: Creare un endpoint privato gestito per connettersi ai risultati dell'origine dati.
description: Questo articolo illustra come creare un endpoint privato gestito per le origini dati da un'area di lavoro di Azure Synapse.This article will teach you how to create a Managed private endpoint to your data sources from an Azure Synapse workspace.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428901"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Creare un endpoint privato gestito nell'origine dati (anteprima)Create a Managed private endpoint to your data source (preview)

Questo articolo illustra come creare un endpoint privato gestito per l'origine dati in Azure.This article will teach you how to create a Managed private endpoint to your data source in Azure. Per altre informazioni, vedere [Endpoint privati gestiti.](./synapse-workspace-managed-private-endpoints.md)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passaggio 1: Aprire l'area di lavoro Di Azure Synapse nel portale di AzureStep 1: Open your Azure Synapse workspace in Azure portal

È possibile creare un endpoint privato gestito nell'origine dati da Azure Synapse Studio.You can create a Managed private endpoint to your data source from Azure Synapse Studio. Selezionare la scheda **Panoramica** nel portale di Azure e selezionare **Avvia Synapse Studio**.
![Avviare Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Passaggio 2: Passare alla scheda Reti virtuali gestite in Synapse StudioStep 2: Navigate to the Managed Virtual Networks tab in Synapse Studio

In Azure Synapse Studio selezionare la scheda **Gestisci** nel riquadro di spostamento sinistro. Selezionare **Reti virtuali gestite** e quindi **selezionare Nuovo**.
![Creare un nuovo endpoint privato gestitoCreate a new Managed private endpoint](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Passaggio 3: Selezionare il tipo di origine datiStep 3: Select the data source type

Selezionare il tipo di origine dati. In questo caso, l'origine dati di destinazione è un account ADLS gen2. Selezionare **Continua**.
![Selezionare un tipo di origine dati di destinazione](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Passaggio 4: Immettere le informazioni sull'origine datiStep 4: Enter information about the data source

Nella finestra successiva immettere le informazioni sull'origine dati. In questo esempio viene creato un endpoint privato gestito a un account ADLS gen2. Immettere un **Nome** per l'endpoint privato gestito. Specificare una sottoscrizione di **Azure** e un nome di **account di archiviazione**. Selezionare **Create** (Crea).
![Immettere i dettagli dell'origine dati di destinazione](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Passaggio 5: Verificare che l'endpoint privato gestito sia stato creato correttamenteStep 5: Verify that your Managed private endpoint was successfully created

Dopo aver inviato la richiesta, vedrai il suo stato. Per verificare che la creazione corretta dell'endpoint privato gestito sia stata creata, controllare *lo stato di provisioning*. Potrebbe essere necessario attendere 1 minuto e selezionare **Aggiorna** per aggiornare lo stato del provisioning. È possibile vedere che l'endpoint privato gestito per l'account ADLS gen2 è stato creato correttamente.

È inoltre possibile notare che lo stato di *approvazione* è *in sospeso*. Il proprietario della risorsa di destinazione può approvare o rifiutare la richiesta di connessione all'endpoint privato. Se il proprietario approva la richiesta di connessione all'endpoint privato, viene stabilito un collegamento privato. Se negato, non viene stabilito un collegamento privato.
![Stato della richiesta di creazione dell'endpoint privato gestito](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [endpoint privati gestitiLearn](./synapse-workspace-managed-private-endpoints.md) more about Managed private endpoints