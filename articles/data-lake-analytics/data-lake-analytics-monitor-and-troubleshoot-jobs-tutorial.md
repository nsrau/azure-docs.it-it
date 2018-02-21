---
title: Risolvere i problemi dei processi di Azure Data Lake Analytics con il portale di Azure | Documentazione Microsoft
description: 'Informazioni su come usare il portale di Azure per risolvere i problemi relativi ai processi di Analisi Data Lake. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: b9c7453cc0a94f70d0098ed83e5f127832065a62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Risolvere i problemi dei processi di Analisi di Azure Data Lake mediante il portale di Azure
Informazioni su come usare il portale di Azure per risolvere i problemi relativi ai processi di Analisi Data Lake.

In questa esercitazione verrà impostato un problema relativo a un file di origine mancante e verrà usato il portale di Azure per risolvere il problema.

## <a name="submit-a-data-lake-analytics-job"></a>Inviare un processo di Data Lake Analytics

Inviare il processo U-SQL seguente:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
Il file di origine definito nello script è **/Samples/Data/SearchLog.tsv1**, ma dovrebbe essere modificato in **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>Risolvere i problemi relativi al processo

**Per visualizzare tutti i processi**

1. Nel portale di Azure fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic nel riquadro contenente il nome dell'account di Analisi Data Lake personale.  Viene visualizzato il riepilogo del processo nel riquadro **Gestione processo** .

    ![Azure Data Lake Analytics - Gestione dei processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Nel riquadro Gestione processo viene fornito un riepilogo dello stato del processo. Si noti che è presente un processo non riuscito.
3. Fare clic nel riquadro **Gestione processo** per visualizzare i processi. I processi sono classificati in base agli stati **In esecuzione**, **In coda** e **Terminato**. Il processo non riuscito verrà visualizzato nella sezione **Terminato** e occuperà la prima posizione nell'elenco. Quando sono presenti molti processi, è possibile fare clic su **Filtro** per semplificare l'individuazione dei processi.

    ![Azure Data Lake Analytics - Filtrare i processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Fare clic sul processo non riuscito nell'elenco per aprire i relativi dettagli in un nuovo pannello:

    ![Azure Data Lake Analytics - Processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Si noti il pulsante **Invia di nuovo** . Dopo aver risolto il problema, è possibile inviare nuovamente il processo.
5. Fare clic sulla parte evidenziata nella schermata precedente per aprire i dettagli dell'errore.  Verrà visualizzato qualcosa di simile a quanto segue:

    ![Azure Data Lake Analytics - Dettagli del processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica che la cartella di origine non è disponibile.
6. Fare clic su **Duplicate Script**.
7. Aggiornare il percorso nella riga **FROM** impostando il seguente percorso:

    "/Samples/Data/SearchLog.tsv"
8. Fare clic su **Submit Job**.

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introduzione ad Azure Data Lake Analytics e U-SQL con Visual Studio](data-lake-analytics-u-sql-get-started.md)
* [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
