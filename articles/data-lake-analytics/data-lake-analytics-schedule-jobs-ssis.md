---
title: Pianificare processi U-SQL di Azure Data Lake Analytics tramite SSIS
description: Informazioni su come usare SQL Server Integration Services (SSIS) per pianificare processi U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523172"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Pianificare processi U-SQL tramite SQL Server Integration Services (SSIS)

In questo documento si apprenderà come orchestrare e creare processi U-SQL usando SQL Server Integration Services (SSIS). 

## <a name="prerequisites"></a>Prerequisiti

[Feature Pack di Azure per Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) include l'[attività di Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) e il componente di [gestione delle connessioni di Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017), che consente di connettersi al servizio Azure Data Lake Analytics. Per usare questa attività, assicurarsi di eseguire le operazioni seguenti:

- [Scaricare e installare SQL Server Data Tools (SSDT) per Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Installare il Feature Pack di Azure per Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Attività di Azure Data Lake Analytics

L'attività di Azure Data Lake Analytics consente agli utenti di inviare processi U-SQL all'account Azure Data Lake Analytics. 

[Informazioni su come configurare l'attività di Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Attività di Azure Data Lake Analytics in SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

È possibile ottenere lo script U-SQL da posizioni diverse tramite le attività e le funzioni predefinite di SSIS. Gli scenari seguenti illustrano come configurare gli script U-SQL per diversi casi d'uso.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1: Usare stored procedure e funzioni con valori di tabella per chiamate a script inline

Nell'editor attività di Azure Data Lake Analytics configurare **SourceType** come **DirectInput** e inserire le istruzioni U-SQL in **USQLStatement**.

Per semplificare la manutenzione e la gestione del codice, inserire solo script U-SQL brevi come script inline. È ad esempio possibile chiamare stored procedure e funzioni con valori di tabella esistenti nei database U-SQL. 

![Modificare uno script U-SQL inline nell'attività SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Articolo correlato: [Come passare parametri a stored procedure](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2: Usare file U-SQL in Azure Data Lake Store

È anche possibile usare file U-SQL in Azure Data Lake Store tramite l'**attività File system di Azure Data Lake Store** nel Feature Pack di Azure. Questo approccio consente di usare gli script archiviati nel cloud.

Eseguire i passaggi seguenti per configurare la connessione tra l'attività File system di Azure Data Lake Store e l'attività di Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Impostare il flusso di controllo delle attività

Nella visualizzazione struttura del pacchetto SSIS aggiungere un'**attività File system di Azure Data Lake Store**, un **contenitore del ciclo ForEach** e un'**attività di Azure Data Lake Analytics** nel contenitore del ciclo ForEach. L'attività File system di Azure Data Lake Store consente di scaricare file U-SQL in una cartella temporanea dell'account ADLS. Il contenitore del ciclo ForEach e l'attività di Azure Data Lake Analytics consentono di inviare i file U-SQL nella cartella temporanea all'account Azure Data Lake Analytics come processo U-SQL.

![Usare file U-SQL in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configurare l'attività File system di Azure Data Lake Store

1. Impostare **Operation** (Operazione) su **CopyFromADLS**.
2. Configurare **AzureDataLakeConnection**. Per altre informazioni, vedere [Gestione connessioni di Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Impostare **AzureDataLakeDirectory**. Definire un puntatore alla cartella in cui sono archiviati gli script U-SQL. Usare il percorso relativo alla cartella radice dell'account Azure Data Lake Store.
4. Impostare **Destination** (Destinazione) su una cartella che memorizza nella cache gli script U-SQL scaricati. Questo percorso di cartella verrà usato nel contenitore del ciclo ForEach per l'invio di processi U-SQL. 

![Configurare l'attività File system di Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Altre informazioni sull'attività File system di Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Configurare il contenitore del ciclo ForEach

1. Nella pagina **Raccolta** impostare **Enumeratore** su **Enumeratore Foreach File**.

2. Impostare **Cartella** nel gruppo **Configurazione enumeratore** sulla cartella temporanea che include gli script U-SQL scaricati.

3. Impostare **File** in **Configurazione enumeratore** su `*.usql` in modo che il contenitore del ciclo rilevi solo i file che terminano con `.usql`.

    ![Configurare il contenitore del ciclo ForEach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Nella pagina **Mapping variabili** aggiungere una variabile definita dall'utente per ottenere il nome per ogni file U-SQL. Impostare **Indice** su 0 per ottenere il nome file. In questo esempio definire una variabile denominata `User::FileName`. Questa variabile verrà usata per ottenere la connessione ai file degli script U-SQL e impostare il nome del processo U-SQL nell'attività di Azure Data Lake Analytics in modo dinamico.

    ![Configurare il contenitore del ciclo ForEach per ottenere il nome file](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configurare l'attività di Azure Data Lake Analytics 

1. Impostare **SourceType** su **FileConnection**.

2. Impostare **FileConnection** sulla connessione file che punta agli oggetti di file restituiti dal contenitore del ciclo ForEach.
    
    Per creare questa connessione file:

   1. Scegli  **\<nuova connessione... >** nell'impostazione FileConnection.
   2. Impostare **Tipo di utilizzo** su **File esistente**e **File** sul percorso di qualsiasi file esistente.

       ![Configurare il contenitore del ciclo ForEach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Nella visualizzazione **Gestioni connessioni** fare con il pulsante destro del mouse sulla connessione file appena creata e scegliere **Proprietà**.

   4. Nella finestra **Proprietà** espandere **Espressioni** e impostare **ConnectionString** sulla variabile definita nel contenitore del ciclo ForEach, ad esempio `@[User::FileName]`.

       ![Configurare il contenitore del ciclo ForEach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Impostare **AzureDataLakeAnalyticsConnection** sull'account Azure Data Lake Analytics a cui inviare i processi. Altre informazioni sul componente di [gestione delle connessioni di Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Impostare altre configurazioni per i processi. [Altre informazioni](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Usare **Espressioni** per impostare in modo dinamico il nome del processo U-SQL:

    1. Nella pagina **Espressioni** aggiungere una nuova coppia chiave-valore dell'espressione per **JobName**.
    2. Impostare il valore per JobName sulla variabile definita nel contenitore del ciclo ForEach, ad esempio `@[User::FileName]`.
    
        ![Configurare l'espressione SSIS per il nome del processo U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3: Usare file U-SQL in Archiviazione BLOB di Azure

È possibile usare file U-SQL in Archiviazione BLOB di Azure tramite l'**attività di download di BLOB di Azure** nel Feature Pack di Azure. Questo approccio consente di usare gli script nel cloud.

I passaggi sono simili a [Scenario 2: Usare file U-SQL in Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Modificare l'attività File system di Azure Data Lake Store nell'attività di download di BLOB di Azure. [Altre informazioni sull'attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Il flusso di controllo è simile al seguente.

![Usare file U-SQL in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4: Usare file U-SQL nel computer locale

Oltre ai file U-SQL archiviati nel cloud, è possibile usare anche file nel computer locale o file distribuiti con i pacchetti SSIS.

1. Fare clic con il pulsante destro del mouse su **Gestioni connessioni** nel progetto SSIS e scegliere **Nuova gestione connessione**.

2. Selezionare il tipo **File** e fare clic su **Aggiungi**.

3. Impostare **Tipo di utilizzo** su **File esistente** e **File** sul file nel computer locale.

    ![Aggiungere una connessione al file locale](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Aggiungere l'attività di **Azure Data Lake Analytics** e:
    1. Impostare **SourceType** su **FileConnection**.
    2. Impostare **FileConnection** sulla connessione file appena creata.

5. Completare le altre configurazioni per l'attività di Azure Data Lake Analytics.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5: Usare istruzioni U-SQL nella variabile SSIS

In alcuni casi può essere necessario generare le istruzioni U-SQL in modo dinamico. È possibile usare una **variabile SSIS** con un'**espressione SSIS** e altre attività SSIS, come Attività Script, per generare l'istruzione U-SQL in modo dinamico.

1. Aprire la finestra degli strumenti Variabili tramite il menu di primo livello **SSIS > Variabili**.

2. Aggiungere una variabile SSIS e impostare il valore direttamente oppure usare l'opzione **Espressione** per generare il valore.

3. Aggiungere l'**attività di Azure Data Lake Analytics** e:
    1. Impostare **SourceType** su **Variable**.
    2. Impostare **SourceVariable** sulla variabile SSIS appena creata.

4. Completare le altre configurazioni per l'attività di Azure Data Lake Analytics.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6: Passare parametri allo script U-SQL

In alcuni casi può essere necessario impostare il valore della variabile U-SQL nello script U-SQL in modo dinamico. La funzionalità **Mapping parametri** nell'attività di Azure Data Lake Analytics è utile in questo scenario. In genere esistono due tipici casi d'uso:

- Impostare le variabili del percorso file di input e output in modo dinamico in base alla data e all'ora correnti.
- Impostare il parametro per le stored procedure.

[Altre informazioni su come impostare i parametri per lo script U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire pacchetti SSIS in Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Feature Pack di Azure per Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Pianificare processi U-SQL tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

