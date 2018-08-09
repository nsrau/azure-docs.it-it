---
title: Come testare il codice di Azure Data Lake Analytics | Microsoft Docs
description: Ulteriori informazioni sull'aggiunta di test case per U-SQL e codice C# esteso per Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: fa5c113541452a93c25adc7c14bdaa6994434c71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365861"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testare il codice Azure Data Lake Analytics

Azure Data Lake fornisce il linguaggio U-SQL, che combina codice SQL dichiarativo a codice C# imperativo per elaborare i dati a qualsiasi scala. Questo documento spiega come creare test case per codice U-SQL e UDO (operatore definito dall'utente) C# esteso.

## <a name="test-u-sql-scripts"></a>Testare gli script U-SQL

Lo script U-SQL viene compilato e ottimizzato per codice eseguibile da eseguire su macchine nel cloud o nel computer locale. Il processo di compilazione e ottimizzazione considera l'intero script U-SQL nel suo complesso. Non è possibile eseguire uno "unit test" tradizionale per ogni istruzione. Tuttavia, usando l'U-SQL test SDK in esecuzione locale è possibile eseguire test a livello di script.

### <a name="create-test-cases-for-u-sql-script"></a>Creare test case per lo script U-SQL

Strumenti Azure Data Lake per Visual Studio consente di creare test case dello script U-SQL.

1.  Fare clic con il pulsante destro del mouse su uno script U-SQL in Esplora soluzioni, quindi selezionare **Crea unit test**.
2.  Creare un nuovo progetto di test o inserire il test case in un progetto di test esistente.

    ![Strumenti Data Lake per Visual Studio -- crea un progetto di test U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Strumenti Data Lake per Visual Studio -- crea una configurazione di progetto di test U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Gestire l'origine dei dati di test

Quando si testano script U-SQL, sono necessari file di input di test. È possibile gestire i dati di test configurando **Test origine dati** nelle proprietà del progetto U-SQL. 

Quando si chiama l'interfaccia `Initialize()` nell'U-SQL test SDK, viene creata una cartella radice di dati locali temporanei nella directory di lavoro del progetto di test e tutti i file e le sottocartelle (e file in esse contenuti) nella cartella di origine dei dati di test vengono copiati nella cartella radice dei dati locali prima di eseguire i test case dello script U-SQL. È possibile aggiungere più cartelle di origine dei dati di test suddividendo il relativo percorso con un punto e virgola.

![Strumenti Data Lake per Visual Studio -- configura l'origine dei dati di test del progetto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Gestire l'ambiente di database a scopo di test

Se gli script U-SQL usano o eseguono query con oggetti di database U-SQL, ad esempio quando si chiamano stored procedure, è necessario inizializzare l'ambiente di database prima di eseguire test case U-SQL. L'interfaccia `Initialize()` nell'U-SQL test SDK consente di distribuire tutti i database a cui fa riferimento il progetto U-SQL nella cartella radice dei dati locali temporanei nella directory di lavoro del progetto di test. 

Ulteriori informazioni sulla [gestione dei riferimenti al progetto di database U-SQL per un progetto U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificare i risultati del test

L'interfaccia `Run()` restituisce un risultato di esecuzione progetto. 0 indica un risultato positivo, 1 indica un risultato negativo. È possibile usare anche funzioni di asserzione C# per verificare gli output. 

### <a name="run-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Un progetto di test dello script U-SQL viene creato su un framework di unit test C#. Dopo aver compilato il progetto, è possibile eseguire tutti i test case attraverso **Esplora test > Playlist**. In alternativa, fare clic con il pulsante destro del mouse sul file .cs, quindi selezionare **Esegui test**.

## <a name="test-c-udos"></a>UDO di test C#

### <a name="create-test-cases-for-c-udos"></a>Creare test case per UDO C#

È possibile usare un framework di unit test C# per testare gli operatori UDO C#. Quando si testano gli UDO, è necessario preparare gli oggetti **IRowset** corrispondenti come input.

È possibile creare un oggetto IRowset in due modi:

- Caricare dati da un file per creare IRowset:

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

- Usare i dati da una raccolta dati per creare IRowset:

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

Dopo aver chiamato le funzioni UDO, è possibile verificare i risultati attraverso la verifica dello schema e del valore Rowset usando le funzioni Assert C#. È possibile usare codice di esempio in un progetto di esempio di unit test U-SQL C# UDO attraverso **File > Nuovo > Progetto** in Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Dopo aver compilato il progetto di test, è possibile eseguire tutti i test case attraverso **Esplora test > Playlist** o fare clic con il pulsante destro del mouse sul file .cs e scegliere **Esegui test**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Eseguire test case in Visual Studio Team Services

Entrambi i **progetti di test di script U-SQL** e i **progetti di test UDO C#** ereditano i progetti di unit test C#. L'[Attività di test di Visual Studio](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) in Visual Studio Team Services può eseguire questi test case. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Eseguire test case U-SQL in Visual Studio Team Services

Per un test U-SQL, assicurarsi di caricare `CPPSDK` nel computer di compilazione, quindi passare il percorso `CPPSDK` in USqlScriptTestRunner(cppSdkFolderFullPath: @"").

**Definizione di CppSDK**

CPPSDK è un pacchetto che include Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0. È l'ambiente richiesto dal runtime U-SQL. È possibile ottenere questo pacchetto nella cartella di installazione di Strumenti Azure Data Lake per Visual Studio:

- Per Visual Studio 2015, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Per Visual Studio 2017, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Preparare CPPSDK nell'agente di compilazione di Visual Studio Team Services**

Il modo più comune per preparare la dipendenza CPPSDK in Visual Studio Team Service è il seguente:

1.  Comprimere la cartella che include le librerie CPPSDK.
2.  Archiviare il file .zip nel sistema di controllo di origine. (Il file .zip consente di archiviare tutte le librerie nella cartella CPPSDK in modo che nessun file venga ignorato da ".gitignore".)   
3.  Decomprimere il file .zip nella pipeline di compilazione.
4.  Indirizzare `USqlScriptTestRunner` a questa cartella non compressa nel computer di compilazione.

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>Eseguire i test case UDO C# in Visual Studio Team Services

Per un test UDO C#, assicurarsi di fare riferimento ai seguenti assembly, necessari per gli UDO. Se vengono referenziati tramite [il pacchetto Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), assicurarsi di aggiungere un'attività di ripristino NuGet nella pipeline di compilazione.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Passaggi successivi

- [How to set up CI/CD pipeline for Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md) (Come configurare la pipeline CI/CD per Azure Data Lake Analytics)
- [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
- [Usare il progetto di database U-SQL per sviluppare il database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

