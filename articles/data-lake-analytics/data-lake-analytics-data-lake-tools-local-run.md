---
title: Eseguire test e debug di processi U-SQL tramite l&quot;esecuzione locale e l&quot;SDK U-SQL di Azure Data Lake | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio e l&quot;SDK U-SQL di Azure Data Lake per eseguire test e debug dei processi di U-SQL nella workstation locale.
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 7e8aed4f56471bb2946c610ca63b0ec50ee1b57e
ms.lasthandoff: 03/21/2017


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake

È possibile usare Strumenti di Azure Data Lake per Visual Studio e l'SDK U-SQL di Azure Data Lake per eseguire i processi di U-SQL nella workstation, esattamente come nel servizio Azure Data Lake. Queste due funzionalità eseguite localmente permettono di risparmiare tempo per le operazioni di test e debug dei processi di U-SQL.

Prerequisiti:

- Un account di Azure Data Lake Analytics. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Strumenti di Azure Data Lake per Visual Studio. Vedere [Sviluppare script U-SQL con gli strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- L'esperienza di sviluppo degli script U-SQL. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).


## <a name="understand-the-data-root-folder-and-the-file-path"></a>Comprendere la cartella data-root e il percorso dei file

Sia l'SDK di U-SQL che l'esecuzione locale richiedono una cartella data-root. La cartella data-root è un "archivio locale" per l'account di calcolo locale, equivalente all'account di Azure Data Lake Store di un account Data Lake Analytics. Il passaggio a un'altra cartella data-root è identico al passaggio a un account archivio differente. Se si vuole accedere a dati comunemente condivisi con cartelle data-root diverse, è necessario usare percorsi assoluti negli script. In alternativa è possibile creare collegamenti simbolici del file system (ad esempio **mklink** in un sistema NTFS) nella cartella data-root che puntino ai dati condivisi.

La cartella data-root viene usata per:

- Archiviare i metadati, inclusi database, tabelle, funzione con valori di tabella e assembly.
- Cercare i percorsi di input e output che sono definiti come percorsi relativi in U-SQL. L'uso di percorsi relativi semplifica la distribuzione dei progetti di U-SQL in Azure.

Negli script U-SQL è possibile usare sia un percorso relativo sia un percorso assoluto locale. Il percorso relativo è relativo al percorso della cartella data-root specificato. È consigliabile usare "/" come separatore del percorso per rendere gli script compatibili con il lato server. Di seguito sono riportati alcuni esempi di percorsi relativi e dei loro percorsi assoluti equivalenti. In questi esempi la cartella data-root è C:\LocalRunDataRoot.

|Percorso relativo|Percorso assoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usare l'esecuzione locale per Visual Studio

Gli Strumenti di Data Lake per Visual Studio offrono un'esperienza di esecuzione locale di U-SQL in Visual Studio. Questa funzionalità permette di:

- Eseguire uno script U-SQL in locale, insieme agli assembly C#.
- Eseguire il debug di un assembly C# localmente.
- Creare, visualizzare ed eliminare i cataloghi di U-SQL (database locali, assembly, schemi e tabelle) da Esplora server. Il catalogo locale è disponibile anche in Esplora server.

    ![Catalogo locale dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Il programma di installazione di Strumenti di Data Lake crea la cartella C:\LocalRunRoot che diventa la cartella data-root predefinita. Il parallelismo di esecuzione locale predefinito è 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Configurare l'esecuzione locale in Visual Studio

1. Aprire Visual Studio.
2. Aprire **Esplora server**.
3. Espandere **Azure** > **Data Lake Analytics**.
4. Aprire il menu **Data Lake** e fare clic su **Opzioni e impostazioni**.
5. Nella struttura ad albero a sinistra espandere **Azure Data Lake** e **Generale**.

    ![Impostazioni di configurazione dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Per poter eseguire la funzionalità di esecuzione locale è richiesto un progetto U-SQL di Visual Studio. Questa parte è diversa rispetto all'esecuzione di script U-SQL da Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Eseguire uno script U-SQL localmente
1. Aprire il progetto U-SQL in Visual Studio.   
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su uno script U-SQL e selezionare **Invia script**.
3. Selezionare **(Locale)** come account di Analytics per eseguire lo script in locale.
È anche possibile fare clic sull'account **(Locale)** nella parte superiore della finestra dello script, quindi selezionare **Invia** (o usare la combinazione di tasti CTRL+F5).

    ![Processi di invio dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Eseguire il debug degli script e delle assembly C# in locale

È possibile eseguire il debug degli assembly C# senza inviarli e registrarli al servizio Azure Data Lake Analytics. È possibile impostare dei punti di interruzione sia nei file dietro il codice, sia nel progetto C# a cui si fa riferimento.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Per eseguire il debug del codice locale nel file code-behind

1. Impostare dei punti di interruzione nel file dietro il codice.
2. Premere F5 per eseguire il debug dello script in locale.

> [!NOTE]
   > La procedura seguente funziona solo in Visual Studio 2015. Nella versione precedente di Visual Studio potrebbe essere necessario aggiungere manualmente i file .pdb.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Per eseguire il debug del codice locale in un progetto C# a cui si fa riferimento

1. Creare un progetto Assembly C# e compilarlo per generare l’output dll.
2. Registrare la dll utilizzando un'istruzione U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Impostare i punti di interruzione nel codice C#.
4. Premere F5 per eseguire il debug dello script con riferimento alla DLL C# in locale.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Usare l'esecuzione locale dall'SDK U-SQL di Data Lake

Oltre a eseguire gli script U-SQL localmente con Visual Studio, è possibile usare anche l'SDK U-SQL di Azure Data Lake per eseguire gli script U-SQL localmente con le interfacce della riga di comando e di programmazione. In questo modo è possibile scalare il test locale di U-SQL.

Sono disponibili altre informazioni sull'[SDK U-SQL di Azure Data Lake](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).
* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
* Per registrare informazioni di diagnostica, vedere [Accesso ai log di diagnostica per Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Per visualizzare una query più complessa, vedere [Analizzare i log dei siti Web con Analisi Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

