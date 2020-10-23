---
title: Creare & query Azure Data Lake Analytics-portale di Azure
description: Usare il portale di Azure per creare un account Azure Data Lake Analytics e inviare un processo U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 6e51c6848476147073626119689407ebc02c036d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220959"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Introduzione ad Azure Data Lake Analytics con il portale di Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Questo articolo descrive come usare il portale di Azure per creare account Azure Data Lake Analytics, definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di una **sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

A questo punto verranno creati un account Data Lake Analytics e un account Azure Data Lake Storage Gen1 contemporaneamente.  Questo passaggio è semplice e richiede solo 60 secondi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa**  >   **dati e analisi**  >  **Data Lake Analytics**.
3. Selezionare i valori per gli elementi seguenti:
   * **Nome**: il nome dell'account di Data Lake Analytics deve contenere solo lettere minuscole e numeri.
   * **Sottoscrizione**: scegliere la sottoscrizione di Azure usata per l'account di Analytics.
   * **Gruppo di risorse**. Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo.
   * **Località**. Selezionare un data center di Azure per l'account di Data Lake Analytics.
   * **Data Lake Storage Gen1**: seguire le istruzioni per creare un nuovo account Data Lake Storage Gen1 o selezionarne uno esistente. 
4. Selezionare eventualmente un piano tariffario per l'account di Data Lake Analytics.
5. Fare clic su **Crea**. 


## <a name="your-first-u-sql-script"></a>Il primo script U-SQL

Il testo seguente è uno script U-SQL molto semplice. Definisce un set di dati di piccole dimensioni nello script e quindi scrive il set di dati nell'account Data Lake Storage Gen1 predefinito come file denominato `/data.csv`.

```usql
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Inviare un processo U-SQL

1. Dall'account Data Lake Analytics selezionare **Nuovo processo**.
2. Incollare il testo dello script U-SQL precedente. Assegnare un nome al processo. 
3. Selezionare il pulsante **Invia** per avviare il processo.   
4. Monitorare lo **stato** del processo e attendere che lo stato del processo diventi **Completato**.
5. Selezionare la scheda **dati** , quindi selezionare la scheda **output** . Selezionare il file di output denominato `data.csv` e visualizzare i dati di output.

## <a name="see-also"></a>Vedere anche

* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per le attività di gestione, vedere [manage Azure Data Lake Analytics using portale di Azure](data-lake-analytics-manage-use-portal.md).
