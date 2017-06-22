---
title: Introduzione ad Azure Data Lake Analytics con il portale di Azure | Documentazione Microsoft
description: 'Informazioni su come usare il portale di Azure per creare un account Data Lake Analytics, creare un processo di Data Lake Analytics con U-SQL e inviare il processo. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare il portale di Azure per creare account Azure Data Lake Analytics, definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessaria una **sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

A questo punto verranno creati un account di Data Lake Analytics e un account di Data Lake Store contemporaneamente.  Questo passaggio è semplice e richiede solo 60 secondi circa.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** >  **Intelligence e analisi** > **Data Lake Analytics**.
3. Selezionare i valori per gli elementi seguenti:
   * **Nome**: il nome dell'account di Data Lake Analytics deve contenere solo lettere minuscole e numeri.
   * **Sottoscrizione**: scegliere la sottoscrizione di Azure usata per l'account di Analytics.
   * **Gruppo di risorse**. Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo.
   * **Località**. Selezionare un data center di Azure per l'account di Data Lake Analytics.
   * **Data Lake Store**: seguire le istruzioni per creare un nuovo account Data Lake Store o selezionarne uno esistente. 
4. Selezionare eventualmente un piano tariffario per l'account di Data Lake Analytics.
5. Fare clic su **Crea**. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Creare e inviare processi di Analisi Data Lake
Dopo aver preparato i dati di origine, è possibile iniziare a sviluppare uno script U-SQL.  

**Per inviare un processo**

1. Dall'account di Data Lake Analytics fare clic su **Nuovo processo**.
2. Immettere il **Nome processo**e lo script U-SQL seguente:

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


Questo script U-SQL legge il file di dati di origine tramite **Extractors.Tsv()** e quindi crea un file CSV tramite **Outputters.Csv()**.

1. Fare clic su **Submit Job**.   
2. Attendere finché lo stato del processo non viene modificato in **Riuscito**.
3. In caso di esito negativo del processo, vedere [Monitorare e risolvere i problemi dei processi di Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
4. Fare clic sulla scheda **Output** e quindi su `SearchLog-from-Data-Lake.csv`. 

## <a name="see-also"></a>Vedere anche

* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).

