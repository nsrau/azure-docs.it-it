---
title: Query Azure Data Lake Analytics - Visual Studio
description: Informazioni su come installare Strumenti Data Lake per Visual Studio e come sviluppare e testare script U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260345"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake e gli strumenti di Analisi di flusso includono funzionalità correlate a due servizi di Azure, Azure Data Lake Analytics e Analisi di flusso di Azure. Per altre informazioni sugli scenari di Analisi di flusso di Azure, vedere Strumenti di Analisi di flusso di Azure per Visual Studio.For more information about the Azure [Stream Analytics scenarios,](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)see Azure Stream Analytics tools for Visual Studio .

Questo articolo descrive come usare Visual Studio per creare account di Azure Data Lake Analytics.This article describes how to use Visual Studio to create Azure Data Lake Analytics accounts. È possibile definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md)e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> È consigliabile eseguire l'aggiornamento ad Azure Data Lake Tools per Visual Studio versione 2.3.3000.4 o successiva. Le versioni precedenti non sono più disponibili per il download e sono deprecate.
>
> 1. Controllare se si stia usando una versione di Strumenti Azure Data Lake per Visual Studio precedente alla 2.3.3000.4.
>
>    ![Controllare la versione degli strumenti](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Se la versione è precedente alla 2.3.3000.4, eseguire l'aggiornamento di Strumenti Azure Data Lake per Visual Studio visitando l'Area download:
>    - [Per Visual Studio 2017 e 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Per Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio**: sono supportate tutte le versioni ad eccezione della versione Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK per .NET** versione 2.7.1 o successiva. Eseguire l'installazione usando [Installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Un account **Data Lake Analytics**. Per creare un account, vedere [Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installare Strumenti Azure Data Lake per Visual Studio

Per questa esercitazione è necessario che sia installato Data Lake Tools per Visual Studio. Per ulteriori informazioni, vedere [Installare Data Lake Tools per Visual Studio.](data-lake-analytics-data-lake-tools-install.md)

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Connettersi a un account Azure Data Lake Analytics

1. Aprire Visual Studio.

1. Aprire **Esplora server** selezionando **Visualizza** > Esplora**server**.

1. Fare clic con il pulsante destro del mouse su **Azure**, quindi **selezionare Connetti a sottoscrizione di Microsoft Azure**. In **Accedi al tuo account,** segui le istruzioni.

1. In **Esplora server**selezionare **Azure** > **Data Lake Analytics**. Verrà visualizzato un elenco degli account Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Scrivere il primo script U-SQL

Il testo seguente è un semplice script U-SQL. Definisce un set di dati di piccole dimensioni e scrive tale set di dati nell'istanza predefinita di Data Lake Store come file denominato `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
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

## <a name="submit-a-data-lake-analytics-job"></a>Inviare un processo di Data Lake Analytics

1. In Visual Studio selezionare **File** > **nuovo** > **progetto**.

1. Selezionare il tipo di **progetto U-SQL** e quindi **avanti**. In **Configura il nuovo progetto**selezionare **Crea**.

   Visual Studio crea una soluzione che contiene un file **Script.usql.Visual** Studio creates a solution that contains a Script.usql file.

1. Incollare lo script da [Scrivere il primo script U-SQL](#write-your-first-u-sql-script) nella finestra **Script.usql.**

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse su **Script.usql**e scegliere **Invia script**.

1. In **Invia processo**scegliere l'account Data Lake Analytics e selezionare **Invia**.

   ![Invio progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Dopo che il processo è stato inviato, viene aperta la scheda **Visualizzazione del processo** che mostra lo stato dei processi.

* **Riepilogo processo** mostra il riepilogo del processo.
* **Grafico del processo** visualizza l'andamento del processo.
* **Operazioni metadati** mostra tutte le azioni eseguite nel catalogo U-SQL.
* **Dati** mostra tutti gli input e gli output.
* **Cronologia dello stato** mostra i dettagli sulla sequenza temporale e sullo stato.
* **AU Analysis** mostra quante OU sono state utilizzate nel lavoro ed esplora le simulazioni delle diverse strategie di allocazione DELL'UA.
* **Diagnostica** fornisce un'analisi avanzata per l'ottimizzazione dell'esecuzione e delle prestazioni del processo.

![Grafico delle prestazioni del processo di Analisi Data Lake per Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Per visualizzare lo stato del processo più recente e aggiornare la schermata, selezionare **Aggiorna**.

## <a name="check-job-status"></a>Controllare lo stato del processo

1. In **Esplora server**selezionare **Azure** > **Data Lake Analytics**.

1. Espandere il nome dell'account Data Lake Analytics.

1. Fare doppio clic su **Processi**.

1. Selezionare il processo inviato in precedenza.

## <a name="see-the-job-output"></a>Visualizzare l'output del processo

1. In **Esplora server**passare al processo inviato.

1. Fare clic sulla scheda **Dati**.

1. Nella scheda **Output del processo** selezionare il file `"/data.csv"`.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire script U-SQL nella workstation per test e debug](data-lake-analytics-data-lake-tools-local-run.md)
* [Eseguire il debug di codice C# in processi U-SQL con Strumenti Azure Data Lake per Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usare gli strumenti di Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
