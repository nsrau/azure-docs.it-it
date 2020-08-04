---
title: "Esercitazione: Introduzione ad Azure Synapse Analytics - Visualizzare i dati dell'area di lavoro con Power BI"
description: In questa esercitazione viene illustrato come creare un'area di lavoro di Power BI, collegare l'area di lavoro Azure Synapse e creare un set di dati Power BI che usa i dati dell'area di lavoro Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 40a2cd5898ede7fc30db9a8475c5ab7cc68095ff
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337637"
---
# <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI

In questa esercitazione viene illustrato come creare un'area di lavoro di Power BI, collegare l'area di lavoro Azure Synapse e creare un set di dati Power BI che usa i dati dell'area di lavoro Azure Synapse. 

## <a name="overview"></a>Panoramica

A partire dai dati di NYC Taxi, sono stati creati set di dati aggregati in due tabelle:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

È possibile collegare un'area di lavoro di Power BI all'area di lavoro di Azure Synapse, in modo da visualizzare facilmente i dati nell'area di lavoro di Power BI. È possibile modificare i report di Power BI direttamente nell'area di lavoro di Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

1. Accedere a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Creare una nuova area di lavoro di Power BI denominata **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Collegare l'area di lavoro di Azure Synapse alla nuova area di lavoro di Power BI

1. In Synapse Studio passare a **Gestisci** > **Servizi collegati**.
1. Selezionare **Nuovo** > **Connetti a Power BI** e impostare questi campi:

    |Impostazione | Valore consigliato | 
    |---|---|
    |**Nome**|**NYCTaxiWorkspace1**|
    |**Nome area di lavoro**|**NYCTaxiWorkspace1**|

1. Selezionare **Crea**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Creare un set di dati di Power BI che usa i dati dell'area di lavoro di Azure Synapse

1. In Synapse Studio passare a **Develop** > **Power BI** (Sviluppo > Power BI).
1. Passare a **NYCTaxiWorkspace1** > **Set di dati di Power BI** e selezionare **New Power BI dataset** (Nuovo set di dati di Power BI).
1. Passare il puntatore del mouse sul database **SQLDB1** e selezionare **Download .pbids file** (Scarica file .pbids).
1. Aprire il file **PBIDS** scaricato. Verrà aperto Power BI Desktop che si connetterà automaticamente a **SQLDB1** nell'area di lavoro di Azure Synapse.
1. Se viene visualizzata una finestra di dialogo denominata **Database SQL Server**:
    1. Selezionare l'**account Microsoft**.
    1. Selezionare **Accedi** e accedere al proprio account.
    1. Selezionare **Connetti**.
1. All'apertura della finestra di dialogo **Navigator**, selezionare la tabella **PassengerCountStats** e quindi **Carica**.
1. Nella finestra di dialogo **Impostazioni di connessione** selezionare **DirectQuery** > **OK**.
1. Fare clic sul pulsante **Report** a sinistra.
1. Aggiungere un **grafico a linee** al report.
    1. Trascinare la colonna **PassengerCount** in **Visualizzazioni** > **Asse**.
    1. Trascinare le colonne **SumTripDistance** e **AvgTripDistance** in **Visualizzazioni** > **Valori**.
1. Nella scheda **Home** selezionare **Pubblica**.
1. Fare clic su **Salva** per salvare le modifiche.
1. Scegliere il nome file **PassengerAnalysis.pbix**, quindi selezionare **Salva**.
1. In **Selezionare una destinazione** scegliere **NYCTaxiWorkspace1** e quindi fare clic su **Seleziona**.
1. Attendere il completamento della pubblicazione.

### <a name="configure-authentication-for-your-dataset"></a>Configurare l'autenticazione per il set di dati

1. Aprire [powerbi.microsoft.com](https://powerbi.microsoft.com/) e fare clic su **Accedi**.
1. Sul lato sinistro, in **Aree di lavoro** selezionare l'area di lavoro **NYCTaxiWorkspace1**.
1. All'interno dell'area di lavoro individuare un set di dati denominato **Passenger Analysis** e un report denominato **Passenger Analysis**.
1. Passare il puntatore del mouse sul set di dati **PassengerAnalysis**, selezionare il pulsante con i puntini di sospensione (...) e quindi selezionare **Impostazioni**.
1. In **Credenziali origine dati** impostare il campo **Metodo di autenticazione** su **OAuth2** e selezionare **Accedi**.

### <a name="edit-a-report-in-synapse-studio"></a>Modificare un report in Synapse Studio

1. Tornare a Synapse Studio e selezionare **Close and refresh** (Chiudi e aggiorna).
1. Passare all'hub **Develop** (Sviluppo).
1. Passare il puntatore del mouse su **Power BI** e fare clic per aggiornare il nodo **Report di Power BI**.
1. In **Power BI** sono ora visibili gli elementi seguenti:
    * In **NYCTaxiWorkspace1** > **Set di dati di Power BI**, un nuovo set di dati denominato **PassengerAnalysis**.
    * In **NYCTaxiWorkspace1** > **Report di Power BI**, un nuovo report denominato **PassengerAnalysis**.
1. Selezionare il report **PassengerAnalysis**. Il report verrà aperto e sarà possibile modificarlo direttamente all'interno di Synapse Studio.

## <a name="monitor-activities"></a>Monitorare le attività

1. In Synapse Studio passare all'hub **Monitor** (Monitoraggio).
1. In questa posizione è possibile visualizzare una cronologia di tutte le attività che si verificano nell'area di lavoro e quelle attualmente attive.
1. Esaminare **Esecuzioni pipeline**, **Applicazioni Apache Spark** e **Richieste SQL** per visualizzare le attività eseguite nell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitoraggio](get-started-monitor.md)
                                 

