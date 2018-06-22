---
title: Monitorare i processi in Azure Data Lake Analytics con il portale di Azure
description: Questo articolo descrive come usare il portale di Azure per risolvere i problemi relativi ai processi di Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8c61b8736dfb13f0c2c2520f22979ac51646e05f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623656"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Gestire processi in Azure Data Lake Analytics con il portale di Azure

**Per visualizzare tutti i processi**

1. Nel portale di Azure fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic nel riquadro contenente il nome dell'account di Analisi Data Lake personale.  Viene visualizzato il riepilogo del processo nel riquadro **Gestione processo** .

    ![Azure Data Lake Analytics - Gestione dei processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Nel riquadro Gestione processo viene fornito un riepilogo dello stato del processo. Si noti che è presente un processo non riuscito.
3. Fare clic nel riquadro **Gestione processo** per visualizzare i processi. I processi sono classificati in base agli stati **In esecuzione**, **In coda** e **Terminato**. Il processo non riuscito verrà visualizzato nella sezione **Terminato** e occuperà la prima posizione nell'elenco. Quando sono presenti molti processi, è possibile fare clic su **Filtro** per semplificare l'individuazione dei processi.

    ![Azure Data Lake Analytics - Filtrare i processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Fare clic sul processo non riuscito nell'elenco per aprire i relativi dettagli:

    ![Azure Data Lake Analytics - Processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Si noti il pulsante **Invia di nuovo** . Dopo aver risolto il problema, è possibile inviare nuovamente il processo.
5. Fare clic sulla parte evidenziata nella schermata precedente per aprire i dettagli dell'errore.  Verrà visualizzato qualcosa di simile a quanto segue:

    ![Azure Data Lake Analytics - Dettagli del processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica che la cartella di origine non è disponibile.
6. Fare clic su **Duplicate Script**.
7. Aggiornare il percorso nella riga **FROM** impostandolo su:

    "/Samples/Data/SearchLog.tsv"
8. Fare clic su **Submit Job**.

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
