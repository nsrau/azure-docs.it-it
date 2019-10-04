---
title: Come testare il codice di Azure Data Lake Analytics
description: Ulteriori informazioni sull'aggiunta di test case per U-SQL e codice C# esteso per Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913952"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testare il codice Azure Data Lake Analytics

Azure Data Lake fornisce il linguaggio [U-SQL](data-lake-analytics-u-sql-get-started.md) . U-SQL combina SQL dichiarativo C# con la necessità di elaborare i dati su qualsiasi scala. In questo documento si apprenderà come creare test case per U-SQL e il C# codice dell'operatore definito dall'utente esteso (Udo).

## <a name="test-u-sql-scripts"></a>Testare gli script U-SQL

Lo script U-SQL viene compilato e ottimizzato per l'esecuzione del codice eseguibile in Azure o nel computer locale. Il processo di compilazione e ottimizzazione considera l'intero script U-SQL nel suo complesso. Non è possibile eseguire una unit test tradizionale per ogni istruzione. Tuttavia, usando l'U-SQL test SDK in esecuzione locale è possibile eseguire test a livello di script.

### <a name="create-test-cases-for-u-sql-script"></a>Creare test case per lo script U-SQL

Strumenti Azure Data Lake per Visual Studio consente di creare test case dello script U-SQL.

1. Fare clic con il pulsante destro del mouse su uno script U-SQL in Esplora soluzioni, quindi selezionare **Crea unit test**.

1. Creare un nuovo progetto di test o inserire il test case in un progetto di test esistente.

   ![Strumenti Data Lake per Visual Studio -- crea una configurazione di progetto di test U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Gestire l'origine dei dati di test

Quando si testano script U-SQL, sono necessari file di input di test. Per gestire i dati di test, in **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto U-SQL e scegliere **Proprietà**. È possibile immettere un'origine nell' **origine dati di test**.

![Strumenti Data Lake per Visual Studio -- configura l'origine dei dati di test del progetto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Quando si chiama l' `Initialize()` interfaccia nell'SDK di test U-SQL, viene creata una cartella radice dati locale temporanea nella directory di lavoro del progetto di test. Tutti i file e le cartelle nella cartella dell'origine dati di test vengono copiati nella cartella radice dati locali temporanea prima di eseguire i test case dello script U-SQL. È possibile aggiungere più cartelle di origine dei dati di test suddividendo il relativo percorso con un punto e virgola.

### <a name="manage-the-database-environment-for-testing"></a>Gestire l'ambiente di database a scopo di test

Se gli script U-SQL usano o eseguono query con oggetti di database U-SQL, è necessario inizializzare l'ambiente di database prima di eseguire i test case di U-SQL. Questo approccio può essere necessario quando si chiamano stored procedure. L'interfaccia `Initialize()` nell'U-SQL test SDK consente di distribuire tutti i database a cui fa riferimento il progetto U-SQL nella cartella radice dei dati locali temporanei nella directory di lavoro del progetto di test.

Per altre informazioni su come gestire i riferimenti al progetto di database U-SQL per un progetto U-SQL, vedere [riferimento a un progetto di database u-](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)SQL.

### <a name="verify-test-results"></a>Verificare i risultati del test

L'interfaccia `Run()` restituisce un risultato di esecuzione progetto. *0* indica esito positivo e *1 indica un* errore. È possibile usare anche funzioni di asserzione C# per verificare gli output.

### <a name="run-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Un progetto di test dello script U-SQL viene creato su un framework di unit test C#. Dopo aver compilato il progetto, selezionare **test** > **Esplora test**di**Windows** > . È possibile eseguire test case da **Esplora test**. In alternativa, fare clic con il pulsante destro del mouse sul file con estensione cs nel unit test e scegliere **Esegui test**.

## <a name="test-c-udos"></a>UDO di test C#

### <a name="create-test-cases-for-c-udos"></a>Creare test case per UDO C#

È possibile usare un C# framework di unit test per testare C# gli operatori definiti dall'utente (Udo). Quando si testano gli UDO, è necessario preparare gli oggetti **IRowset** corrispondenti come input.

Esistono due modi per creare un oggetto **IRowset** :

- Caricare i dati da un file per creare **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Usare i dati di una raccolta di dati per creare **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verificare i risultati del test

Dopo aver chiamato le funzioni UDO, è possibile verificare i risultati attraverso la verifica dello schema e del valore Rowset usando le funzioni Assert C#. È possibile aggiungere un **progetto di unit C# test Udo di U-SQL** alla soluzione. A tale scopo, selezionare **File > nuovo > progetto** in Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Dopo aver compilato il progetto, selezionare **test** > **Esplora test**di**Windows** > . È possibile eseguire test case da **Esplora test**. In alternativa, fare clic con il pulsante destro del mouse sul file con estensione cs nel unit test e scegliere **Esegui test**.

## Esegui test case in Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Entrambi i **progetti di test di script U-SQL** e i **progetti di test UDO C#** ereditano i progetti di unit test C#. L' [attività di test di Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) in Azure Pipelines può eseguire questi test case.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Eseguire test case U-SQL in Azure Pipelines

Per un test U-SQL, assicurarsi di caricare `CPPSDK` nel computer di compilazione, quindi passare il `CPPSDK` percorso a `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`.

#### <a name="what-is-cppsdk"></a>Che cos'è CPPSDK?

CPPSDK è un pacchetto che include Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0. Questo pacchetto include l'ambiente necessario per il runtime di U-SQL. È possibile ottenere questo pacchetto nella cartella di installazione di Strumenti Azure Data Lake per Visual Studio:

- Per Visual Studio 2015, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Per Visual Studio 2017, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Per Visual Studio 2019, è sotto`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Preparare CPPSDK nell'agente di compilazione Azure Pipelines

Il modo più comune per preparare la dipendenza CPPSDK in Azure Pipelines è il seguente:

1. Zip la cartella che include le librerie CPPSDK.

1. Archiviare il file .zip nel sistema di controllo di origine. Il file zip consente di archiviare tutte le librerie nella cartella CPPSDK in modo che i file non vengano ignorati a causa `.gitignore` di un file.

1. Decomprimere il file .zip nella pipeline di compilazione.

1. Puntare `USqlScriptTestRunner` a questa cartella decompressa nel computer di compilazione.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Esegui C# test case UDO in Azure Pipelines

Per un C# test dell'Udo, assicurarsi di fare riferimento agli assembly seguenti, necessari per Udo.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Se vengono referenziati tramite [il pacchetto Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), assicurarsi di aggiungere un'attività di ripristino NuGet nella pipeline di compilazione.

## <a name="next-steps"></a>Passaggi successivi

- [How to set up CI/CD pipeline for Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md) (Come configurare la pipeline CI/CD per Azure Data Lake Analytics)
- [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
- [Usare il progetto di database U-SQL per sviluppare il database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
