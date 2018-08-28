---
title: Introduzione ad Azure Data Lake Analytics con Visual Studio
description: Informazioni su come installare Strumenti Data Lake per Visual Studio e come sviluppare e testare script U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: get-started-article
ms.date: 08/13/2018
ms.openlocfilehash: 852840fc29589292e7a74390026b78b15f81e721
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "41924829"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare Visual Studio per creare account Azure Data Lake Analytics, definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

>[!IMPORTANT]
> Microsoft consiglia di eseguire l'aggiornamento a Strumenti Azure Data Lake per Visual Studio versione 2.3.3000.4 o successiva. Le versioni precedenti non sono più disponibili per il download e sono deprecate. 
>
>**Cosa occorre fare?**
>
>1. Controllare se si stia usando una versione di Strumenti Azure Data Lake per Visual Studio precedente alla 2.3.3000.4. 
>   
>   ![Controllare la versione degli strumenti](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
>2. Se la versione è precedente alla 2.3.3000.4, eseguire l'aggiornamento di Strumenti Azure Data Lake per Visual Studio visitando l'Area download: 
>    - [Per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Per Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio**: sono supportate tutte le versioni ad eccezione della versione Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK per .NET** versione 2.7.1 o successiva.  Eseguire l'installazione usando [Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Un account **Data Lake Analytics**. Per creare un account, vedere [Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installare Strumenti Azure Data Lake per Visual Studio

Per questa esercitazione è necessario che sia installato Data Lake Tools per Visual Studio. Seguire le [istruzioni di installazione](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Connettersi a un account Azure Data Lake Analytics

1. Aprire Visual Studio.

2. Aprire Esplora server selezionando **Visualizza** > **Esplora Server**.

3. Fare clic con il pulsante destro del mouse su **Azure**. Scegliere quindi **Connetti a sottoscrizione di Microsoft Azure** e seguire le istruzioni.

4. In Esplora server selezionare **Azure** > **Data Lake Analytics**. Verrà visualizzato un elenco degli account Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Scrivere il primo script U-SQL

Il testo seguente è un semplice script U-SQL. Definisce un set di dati di piccole dimensioni e scrive tale set di dati nell'istanza predefinita di Data Lake Store come file denominato `/data.csv`.

```
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

1. Selezionare **File** > **Nuovo** > **Progetto**.

2. Selezionare il tipo di **Progetto U-SQL** e quindi fare clic su **OK**. Visual Studio crea una soluzione con un file **Script.usql**.

3. Incollare lo script precedente nella finestra **Script.usql**.

4. Nell'angolo superiore sinistro della finestra **Script.usql** specificare l'account Data Lake Analytics.

    ![Invio progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. Nell'angolo superiore sinistro della finestra **Script.usql** selezionare **Invia**.

6. Dopo che il processo è stato inviato, viene aperta la scheda **Visualizzazione del processo** che mostra lo stato dei processi. Per visualizzare lo stato più aggiornato del processo e aggiornare la schermata, fare clic su **Aggiorna**.

    ![Grafico delle prestazioni del processo di Analisi Data Lake per Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Riepilogo processo** mostra il riepilogo del processo.   
   * **Grafico del processo** visualizza l'andamento del processo.
   * **Operazioni metadati** mostra tutte le azioni eseguite nel catalogo U-SQL.
   * **Dati** mostra tutti gli input e gli output.
   * **Cronologia dello stato** mostra i dettagli sulla sequenza temporale e sullo stato.
   * **Analisi unità di analisi** mostra quante unità di analisi sono state usate nel processo e consente di esplorare le simulazioni di diverse strategie di allocazione di unità di analisi.
   * **Diagnostica** fornisce un'analisi avanzata per l'ottimizzazione dell'esecuzione e delle prestazioni del processo.

## <a name="check-job-status"></a>Controllare lo stato del processo

1. In Esplora server selezionare **Azure** > **Data Lake Analytics**.

2. Espandere il nome dell'account Data Lake Analytics.

3. Fare doppio clic su **Processi**.

4. Selezionare il processo inviato in precedenza.

## <a name="see-the-job-output"></a>Visualizzare l'output del processo

1. In Esplora server cercare il processo inviato.

2. Fare clic sulla scheda **Dati** .

3. Nella scheda **Output del processo** selezionare il file `"/data.csv"`.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire script U-SQL nella workstation per test e debug](data-lake-analytics-data-lake-tools-local-run.md)
* [Eseguire il debug di codice C# in processi U-SQL con Strumenti Azure Data Lake per Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usare gli strumenti di Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
