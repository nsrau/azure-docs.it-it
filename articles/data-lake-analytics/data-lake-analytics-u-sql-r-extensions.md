---
title: Estendere gli script U-SQL con R in Azure Data Lake Analytics | Microsoft Docs
description: Informazioni su come eseguire il codice R negli script U-SQL
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d479af515566f497d9611e75426f6acb8f8276d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Esercitazione: Introduzione all'estensione di U-SQL con R

L'esempio seguente illustra i passaggi di base per la distribuzione del codice R:
* Usare l'istruzione `REFERENCE ASSEMBLY` per abilitare le estensioni R per lo script U-SQL.
* Usare l'operazione ` REDUCE` per partizionare i dati di input in una chiave.
* Le estensioni R per U-SQL includono un riduttore predefinito (`Extension.R.Reducer`) che esegue il codice R in ogni vertice assegnato al riduttore. 
* Utilizzo dei frame di dati denominati dedicati chiamati rispettivamente `inputFromUSQL` e `outputToUSQL ` per passare dati tra U-SQL e R. I nomi di input e di output dell'identificatore DataFrame sono fissi (vale a dire, gli utenti non possono modificare i nomi predefiniti di input e di output degli identificatori DataFrame).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Incorporare il codice R nello script U-SQL

È possibile incorporare il codice R nello script U-SQL usando il parametro del comando di `Extension.R.Reducer`. Ad esempio, è possibile dichiarare lo script R come una variabile di stringa e passarlo come parametro al Reducer.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Mantenere il codice R in un file separato e farvi riferimento nello script U-SQL

L'esempio seguente illustra un uso più complesso. In questo caso, il codice R è distribuito come una RISORSA che rappresenta lo script U-SQL.

Salvare il codice R come file separato.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Usare uno script U-SQL per distribuire lo script R con l'istruzione DEPLOY RESOURCE.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Come R si integra con U-SQL

### <a name="datatypes"></a>Tipi di dati
* Le colonne di tipo stringa e numeriche di U-SQL vengono convertite così come sono tra DataFrame R e U-SQL [tipi supportati: `double`, `string`, `bool`, `integer`, `byte`].
* Il datatype `Factor` non è supportato in U-SQL.
* `byte[]` deve essere serializzato come `string` con codifica Base 64.
* Le stringhe U-SQL possono essere convertite in fattori nel codice R, una volta che U SQL crea il frame di dati R di input o impostando il parametro riduttore `stringsAsFactors: true`.

### <a name="schemas"></a>Schemi
* I set di dati di U-SQL non possono avere nomi di colonna duplicati.
* I nomi di colonna dei set di dati di U-SQL devono essere stringhe.
* I nomi di colonna devono essere identici in U-SQL e negli script R.
* Le colonne di sola lettura non possono fare parte del frame di dati di output. Poiché le colonne di sola lettura vengono inserite automaticamente nella tabella U-SQL se fanno parte dello schema di output degli oggetti UDO.

### <a name="functional-limitations"></a>Limitazioni funzionali
* Non è possibile creare due volte un'istanza del motore R nello stesso processo. 
* Attualmente, U-SQL non supporta UDO Combiner per la stima mediante modelli partizionati generati usando UDO Reducer. Gli utenti possono dichiarare i modelli partizionati come risorsa e usarli negli script R. Vedere il codice di esempio `ExtR_PredictUsingLMRawStringReducer.usql`.

### <a name="r-versions"></a>Versioni R
È supportato solo R 3.2.2.

### <a name="standard-r-modules"></a>Moduli R standard

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Limitazioni delle dimensioni di input e output
A ogni vertice è assegnata una quantità di memoria limitata, Poiché devono esistere frame di dati di input e di output in memoria nel codice R, le dimensioni totali per l'input e per l'output non possono superare i 500 MB.

### <a name="sample-code"></a>Codice di esempio
Altri esempi di codice sono disponibili nell'account Data Lake Store dopo aver installato le estensioni Advanced Analytics U-SQL. Il percorso per il codice di esempio aggiuntivo è: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Distribuzione dei moduli personalizzati R con U-SQL

Innanzitutto, creare un modulo personalizzato R fare lo zip e quindi caricare il file del modulo personalizzato R compresso in un archivio ADL. Nell'esempio, si caricherà magittr_1.5.zip nella radice dell'account predefinito ADLS dell'account ADLA che si sta usando. Dopo aver caricato il modulo nell'archivio ADL, dichiararlo come quando si usa DEPLOY RESOURCE per renderlo disponibile nello script U-SQL script e chiamare il metodo `install.packages` per installarlo.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomePackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
