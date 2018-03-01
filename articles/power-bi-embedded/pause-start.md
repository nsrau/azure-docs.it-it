---
title: "Sospendere e avviare la capacità di Power BI Embedded nel portale di Azure | Microsoft Docs"
description: "Questo articolo illustra come sospendere e avviare una capacità di Power BI Embedded in Microsoft Azure."
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
ms.openlocfilehash: a7f86ebf3e79812eb50e58cbb320336cbd1149e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Sospendere e avviare la capacità di Power BI Embedded nel portale di Azure

Questo articolo illustra come sospendere e avviare una capacità di Power BI Embedded in Microsoft Azure. Si presuppone che sia stata creata una capacità di Power BI Embedded. In caso contrario, vedere [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creare capacità di Power BI Embedded nel portale di Azure) per iniziare.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="pause-your-capacity"></a>Sospendere la capacità

La sospensione della capacità impedisce di generare addebiti. La sospensione della capacità è molto utile se non è necessario usare la capacità per un periodo di tempo. Eseguire questa procedura per sospendere la capacità.

> [!NOTE]
> La sospensione della capacità potrebbe impedire la visualizzazione dei contenuti in Power BI. Assicurarsi di annullare l'assegnazione delle aree di lavoro dalla capacità prima della sospensione per impedire l'interruzione.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** > **Power BI Embedded** per visualizzare le capacità.

    ![Tutti i servizi nel portale di Azure](media/pause-start/azure-portal-more-services.png)

3. Selezionare la capacità che si vuole sospendere.

    ![Elenco di capacità di Power BI Embedded nel portale di Azure](media/pause-start/azure-portal-capacity-list.png)

4. Selezionare **Sospendi** nei dettagli della capacità.

    ![Sospendere la capacità](media/pause-start/azure-portal-pause-capacity.png)

5. Selezionare **Sì** per confermare che si vuole sospendere la capacità.

    ![Conferma sospensione](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Avviare la capacità

Riprendere l'uso avviando la capacità. Quando si avvia la capacità, riprende anche la fatturazione.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** > **Power BI Embedded** per visualizzare le capacità.

    ![Tutti i servizi nel portale di Azure](media/pause-start/azure-portal-more-services.png)

3. Selezionare la capacità che si vuole avviare.

    ![Elenco di capacità di Power BI Embedded nel portale di Azure](media/pause-start/azure-portal-capacity-list.png)

4. Selezionare **Avvia** nei dettagli della capacità.

    ![Avviare la capacità](media/pause-start/azure-portal-start-capacity.png)

5. Selezionare **Sì** per confermare che si vuole avviare la capacità.

    ![Conferma avvio](media/pause-start/azure-portal-confirm-start.png)

Se dei contenuti sono assegnati a questa capacità, saranno disponibili all'avvio.

## <a name="next-steps"></a>Passaggi successivi

Per ridimensionare la capacità, vedere [Ridimensionare la capacità di Power BI Embedded](scale-capacity.md).

Per iniziare a incorporare il contenuto di Power BI nell'applicazione, vedere [Incorporare i dashboard, i report e i riquadri di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)