---
title: "Avvio rapido: Collegamento di un'area di lavoro Power BI a un'area di lavoro di Synapse"
description: Collegare un’area di lavoro Power BI a un'area di lavoro di Azure Synapse Analytics seguendo la procedura descritta in questa guida.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/29/2020
ms.author: jocaplan
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: f9ae7b74c17bb330c2c7aa99903c62d4701f0a52
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274187"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Avvio rapido: Collegamento di un'area di lavoro Power BI a un'area di lavoro di Synapse

In questo argomento di Avvio rapido si apprenderà come connettere un'area di lavoro Power BI a un'area di lavoro Synapse Analytics per creare nuovi report e set di dati Power BI da Synapse Studio (anteprima).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Creare un'area di lavoro di Azure Synapse e un account di archiviazione associato](quickstart-create-workspace.md)
- [Area di lavoro Power BI Professional o Premium](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Collegare un’area di lavoro Power BI all’area di lavoro Synapse

1. A partire da Synapse Studio, fare clic su **Gestisci**.

    ![Clic su Gestisci di Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. In **Connessioni esterne**, fare clic su **Servizi collegati**.

    ![Servizi collegati evidenziato.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Fare clic su **+ Nuovo**.

    ![+Nuovo per i servizi collegati evidenziato.](media/quickstart-link-powerbi/new-highlighted.png)

4. Fare clic su **Power BI** e fare clic su **Continua**.

    ![Visualizzazione del servizio collegato Power BI.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Immettere un nome per il servizio collegato e selezionare un'area di lavoro dall'elenco a discesa.

    ![Visualizzazione della configurazione del servizio collegato Power BI.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Fare clic su **Crea**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Visualizzare l’area di lavoro Power BI in Synapse Studio

Una volta collegate le aree di lavoro, è possibile esplorare i set di dati Power BI, modificare/creare nuovi report Power BI da Synapse Studio.

1. Fare clic su **Sviluppa**.

    ![Clic su Sviluppa di Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Espandere Power BI e l'area di lavoro che si desidera utilizzare.

    ![Espandere Power BI e l'area di lavoro.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

È possibile creare nuovi report facendo clic su **+** nella parte superiore della scheda **Sviluppa**. È possibile modificare i report esistenti facendo clic sul nome del report. Eventuali modifiche salvate verranno riscritte nell'area di lavoro Power BI.

![Visualizzare e modificare report di Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [creare il report di Power BI sui file archiviati in Archiviazione di Azure](sql/tutorial-connect-power-bi-desktop.md).
