---
title: Ripristinare un pool SQL dedicato esistente
description: Guida alle procedure per il ripristino di un pool SQL dedicato esistente.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332101"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Ripristinare un pool SQL dedicato esistente

Questo articolo illustra come ripristinare un pool SQL dedicato esistente in Azure sinapsi Analytics usando portale di Azure e sinapsi Studio. Questo articolo si applica sia ai ripristini che ai ripristini geografici. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Ripristinare un pool SQL dedicato esistente tramite sinapsi Studio

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare all'area di lavoro sinapsi. 
3. In Introduzione-> aprire sinapsi studio e selezionare **Apri**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Nel riquadro di spostamento a sinistra selezionare **dati**.
5. Selezionare **Gestisci pool**. 
6. Selezionare **+ nuovo** per creare un nuovo pool SQL dedicato. 
7. Nella scheda Impostazioni aggiuntive selezionare un punto di ripristino da cui eseguire il ripristino. 

    Se si desidera eseguire un ripristino geografico, selezionare l'area di lavoro e il pool SQL dedicato che si desidera ripristinare. 

8. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. 

    ![Punti di ripristino](../media/sql-pools/restore-point.PNG)

    Se il pool SQL dedicato non contiene punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima di eseguire il ripristino. Per User-Defined punti di ripristino, selezionarne uno esistente o crearne uno nuovo.

    Se si esegue il ripristino di un backup geografico, è sufficiente selezionare l'area di lavoro che si trova nell'area di origine e il pool SQL dedicato che si vuole ripristinare. 

9. Selezionare **Rivedi e crea**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Ripristinare un pool SQL dedicato esistente tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pool SQL dedicato da cui si desidera eseguire il ripristino.
3. Nella parte superiore del pannello della panoramica, selezionare **Ripristina**.

    ![ Panoramica del servizio di ripristino](../media/sql-pools/restore-sqlpool-01.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. 

    Se il pool SQL dedicato non contiene punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima di eseguire il ripristino. 

    Se si desidera eseguire un ripristino geografico, selezionare l'area di lavoro e il pool SQL dedicato che si desidera ripristinare. 

5. Selezionare **Rivedi e crea**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un punto di ripristino](sqlpool-create-restore-point.md)
