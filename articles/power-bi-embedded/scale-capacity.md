---
title: "Ridimensionare la capacità di Power BI Embedded | Microsoft Docs"
description: "Questo articolo illustra come ridimensionare una capacità di Power BI Embedded in Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: e1ab6a2f52fa56f1e04c6c327796587daf43596e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Ridimensionare la capacità di Power BI Embedded

Questo articolo illustra come ridimensionare una capacità di Power BI Embedded in Microsoft Azure. Il ridimensionamento consente di aumentare o diminuire le dimensioni della capacità.

Si presuppone che sia stata creata una capacità di Power BI Embedded. In caso contrario, vedere [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creare capacità di Power BI Embedded nel portale di Azure) per iniziare.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="scale-a-capacity"></a>Ridimensionare una capacità

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Altri servizi** > **Power BI Embedded** per visualizzare le capacità.

    ![Altri servizi nel portale di Azure](media/scale-capacity/azure-portal-more-services.png)

3. Selezionare la capacità a cui si vuole passare.

    ![Elenco di capacità di Power BI Embedded nel portale di Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Selezionare **Piano tariffario** sotto **Piano** nell'ambito della propria capacità.

    ![Opzione Piano tariffario in Piano](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    Il piano tariffario corrente è evidenziato in blu.

    ![Piano tariffario corrente evidenziato in blu](media/scale-capacity/azure-portal-current-tier.png)

5. Per passare a un piano superiore o inferiore, selezionare il nuovo piano a cui passare. Quando si seleziona un nuovo piano, viene visualizzato un bordo blu tratteggiato attorno alla selezione. Selezionare **Seleziona** per passare al nuovo piano.

    ![Selezionare il nuovo piano](media/scale-capacity/azure-portal-select-new-tier.png)

    Il completamento del ridimensionamento della capacità potrebbe richiedere un minuto o due.

6. Verificare il piano visualizzando la scheda Panoramica. Viene elencato il piano tariffario corrente.

    ![Verificare il piano corrente](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Passaggi successivi

Per sospendere o avviare la capacità, vedere [Sospendere e avviare la capacità di Power BI Embedded nel portale di Azure](pause-start.md).

Per iniziare a incorporare il contenuto di Power BI nell'applicazione, vedere [Come incorporare i dashboard, i report e i riquadri di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)