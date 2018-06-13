---
title: Ridimensionare la capacità di Power BI Embedded | Microsoft Docs
description: Questo articolo illustra come ridimensionare una capacità di Power BI Embedded in Microsoft Azure.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408286"
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Ridimensionare la capacità di Power BI Embedded

Questo articolo illustra come ridimensionare una capacità di Power BI Embedded in Microsoft Azure. Il ridimensionamento consente di aumentare o diminuire le dimensioni della capacità.

Si presuppone che sia stata creata una capacità di Power BI Embedded. In caso contrario, vedere [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creare capacità di Power BI Embedded nel portale di Azure) per iniziare.

> [!NOTE]
> Un'operazione di ridimensionamento può richiedere circa un minuto. In questo intervallo di tempo la capacità non è disponibile e potrebbe non essere possibile caricare i contenuti incorporati.

## <a name="scale-a-capacity"></a>Ridimensionare una capacità

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** > **Power BI Embedded** per visualizzare le capacità.

    ![Tutti i servizi nel portale di Azure](media/scale-capacity/azure-portal-more-services.png)

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
