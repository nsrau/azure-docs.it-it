---
title: Come testare il codice di Azure Data Lake Analytics | Microsoft Docs
description: Informazioni su come aggiungere test case relativi all'U-SQL e un codice C# esteso per Azure Data Lake Analytics.
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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890810"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Come testare il codice di Azure Data Lake Analytics

Azure Data Lake offre U-SQL come linguaggio che combina SQL dichiarativo con C# imperativo per consentire di elaborare dati su qualsiasi scala. Questo documento descrive come creare test case per l'U-SQL e un codice UDO C# (operatore definito dall'utente) esteso.

## <a name="test-u-sql-scripts"></a>Testare gli script U-SQL

Lo script U-SQL è compilato e ottimizzato per il codice eseguibile ed eseguito tra più computer nel cloud o nel computer locale. Il processo di compilazione e ottimizzazione considera l'intero script U-SQL nel suo complesso da eseguire. Non è possibile eseguire "unit test" tradizionali per ogni istruzione. Tuttavia, tramite l'SDK di test U-SQL SDK e l'SDK di esecuzione locale, è possibile eseguire test a livello di script.

### <a name="create-test-cases-for-u-sql-script"></a>Creare test case per lo script U-SQL

Strumenti Azure Data Lake per Visual Studio offre un'esperienza ottimale per la creazione di test case per gli script U-SQL.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su uno script U-SQL e selezionare **Create Unit Test** (Crea unit test).
2.  Eseguire la configurazione per creare un nuovo progetto di test o inserire il test case in un progetto di test esistente.

    ![Creare un progetto di test U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Configurazione di un progetto di test U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Gestire l'origine dati di test

Per il test degli script U-SQL sono necessari file di input di test. È possibile gestire questi dati di test configurando il **Test origine dati** nelle proprietà del progetto U-SQL. Quando si chiama l'interfaccia `Initialize()` nell'SDK di test U-SQL, una cartella Data-Root locale temporanea viene creata nella directory di lavoro del progetto di test e tutti i file e le sottocartelle (e i file contenuti nelle sottocartelle) vengono copiati nella cartella di origine dei dati locale temporanea prima di eseguire i test case dello script U-SQL. È possibile aggiungere altre cartelle di origine dati di test suddividendo il percorso della cartella di dati di test con punto e virgola.

![Configurazione dell'origine dati di test del progetto di Strumenti Data Lake per Visual Studio](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Gestire l'ambiente del database per il test

Se gli script U-SQL usano o eseguono query con gli oggetti di database U-SQL, ad esempio stored procedure di chiamata, è necessario inizializzare l'ambiente del database prima di eseguire test case di U-SQL. L'interfaccia `Initialize()` nell'SDK di test di U-SQL consente di distribuire tutti i database cui fa riferimento il progetto U-SQL nella cartella radice dei dati locale temporanea nella directory di lavoro del progetto di test. 

Altre informazioni su [come gestire i riferimenti a progetti di database U-SQL per progetti U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificare i risultati del test

L'interfaccia `Run()` restituisce il risultato di esecuzione del processo: il valore 0 indica un esito positivo, mentre 1 indica un esito negativo. È possibile usare anche funzioni di asserzione C# per verificare gli output. 

### <a name="execute-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Un progetto di test dello script U-SQL si basa su framework di unit test C#. Una volta compilato il progetto, è possibile eseguire tutti i test case tramite **Esplora test > Playlist**, oppure facendo doppio clic sul file .cs e scegliendo **Esegui test**.

## <a name="test-c-udos"></a>UDO di test C#

### <a name="create-test-cases-for-c-udos"></a>Creare test case per UDO C#

È possibile usare framework unit test C# per testare gli UDO (User-Defined Operator) C#. Quando si testa un UDO, è necessario preparare l'oggetto **IRowset** corrispondente come input.

Esistono due modi per creare IRowset:

1.  Caricare dati da un file per creare IRowset

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

2.  Usare dati estratti dalla raccolta dati per creare IRowset

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

Dopo la chiamata di funzioni UDO, è possibile verificare il risultato tramite lo schema e la verifica di valore del set di righe tramite le funzioni C# Assert. Un codice di esempio è consultabile Nel progetto di esempio di unit test UDO U-SQL C# in **File > Nuovo > Progetto** in Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Eseguire test case in Visual Studio

Una volta compilato il progetto di test, è possibile eseguire tutti i test case tramite **Esplora test > Playlist**, oppure facendo doppio clic sul file. cs e scegliendo **Esegui test**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Eseguire test case in Visual Studio Team Services

Sia il **progetto di test dello script U-SQL** che il **progetto di test UDO C#** ereditano il progetto di unit test C#. [Attività di test di Visual Studio](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) in Visual Studio Team Service possono eseguire questi test case. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Eseguire test case U-SQL in Visual Studio Team Services

Per i test U-SQL, assicurarsi di caricare `CPPSDK` nel computer di compilazione e trasferire il percorso `CPPSDK` su USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Definizione di CppSDK**

CPPSDK è un pacchetto che include Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0, l'ambiente necessario per il runtime U-SQL. È possibile ottenere questo pacchetto in nella cartella di installazione di Strumenti Azure Data Lake per Visual Studio:

- Per Visual Studio 2015, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Per Visual Studio 2017, il pacchetto è disponibile in `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Come preparare CPPSDK nell'agente di compilazione di Visual Studio Team Services**

Il modo comune per la preparazione di questa dipendenza CPPSDK in Visual Studio Team Services è il seguente:

1.  Zippare la cartella contenente le librerie CPPSDK.
2.  Archiviare il file zip nel sistema di controllo del codice sorgente. (Lo zip dei file assicura di archiviare tutte le librerie nella cartella CPPSDK, altrimenti alcuni file verranno ignorati da ".gitignore").
3.  Decomprimere i file zip nella pipeline di compilazione.
4.  Indirizzare `USqlScriptTestRunner` a questa cartella non compressa nel computer di compilazione.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Eseguire test case C# UDO in Visual Studio Team Services

Per i test UDO C#, assicurarsi di referenziare gli assembly seguenti, necessari per l'UDO. Se vengono referenziati tramite [il pacchetto Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), assicurarsi di aggiungere un'attività di ripristino NuGet nella pipeline di compilazione.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Passaggi successivi

- [How to set up CI/CD pipeline for Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md) (Come configurare la pipeline CI/CD per Azure Data Lake Analytics)
- [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
- [Usare il progetto di database U-SQL per sviluppare il database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

