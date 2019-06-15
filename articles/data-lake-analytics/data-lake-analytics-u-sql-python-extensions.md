---
title: Estendere gli script U-SQL con Python in Azure Data Lake Analytics
description: Informazioni su come eseguire il codice Python negli script U-SQL usando Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813412"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Estendere gli script U-SQL con il codice Python in Azure Data Lake Analytics

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che le estensioni di Python siano installate nell'account di Azure Data Lake Analytics.

* Passare all'account di Data Lake Analytics nel portale di Azure
* Nel menu a sinistra, in **ATTIVITÀ INIZIALI** fare clic su **Script di esempio**
* Fare clic su **Installa estensioni U-SQL** e quindi su **OK**

## <a name="overview"></a>Panoramica 

Le estensioni Python per U-SQL consentono agli sviluppatori di effettuare esecuzioni parallele elevate del codice Python. L'esempio seguente illustra i passaggi di base:

* Usare l'istruzione `REFERENCE ASSEMBLY` per abilitare le estensioni Python per lo script U-SQL
* Uso dell'operazione `REDUCE` per partizionare i dati di input in una chiave
* Le estensioni Python per U-SQL includono un riduttore predefinito (`Extension.Python.Reducer`) che esegue il codice Python in ogni vertice assegnato al riduttore
* Lo script U-SQL contiene il codice Python incorporato che include una funzione denominata `usqlml_main` che accetta un intervallo di dati Pandas come input e restituisce un intervallo di dati Pandas come output.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Come Python si integra con U-SQL

### <a name="datatypes"></a>Tipi di dati

* Le colonne stringa e numeriche di U-SQL vengono convertite come sono tra Pandas e U-SQL
* I valori Null di U-SQL vengono convertiti in e da valori `NA` di Pandas

### <a name="schemas"></a>Schemi

* I vettori indice di Pandas non sono supportati in U-SQL. Tutti i frame di dati di input della funzione Python hanno sempre un indice numerico a 64 bit compreso tra 0 e il numero di righe meno 1. 
* I set di dati di U-SQL non possono avere nomi di colonna duplicati.
* Nomi di colonna dei set di dati di U-SQL che non sono stringhe. 

### <a name="python-versions"></a>Versioni di Python
È supportato solo Python 3.5.1 (compilato per Windows). 

### <a name="standard-python-modules"></a>Moduli standard di Python
Sono inclusi tutti i moduli di Python standard.

### <a name="additional-python-modules"></a>Altri moduli di Python
Oltre alle librerie di Python standard, sono incluse diverse librerie di Python di uso frequente:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Messaggi di eccezione
Attualmente un'eccezione nel codice Python viene visualizzata come errore di vertice generico. In futuro i messaggi di errore dei processi U-SQL visualizzeranno il messaggio dell'eccezione Python.

### <a name="input-and-output-size-limitations"></a>Limitazioni delle dimensioni di input e output
A ogni vertice è assegnata una quantità di memoria limitata, che attualmente corrisponde a 6 GB per AU. Poiché devono esistere intervalli di dati di input e di output in memoria nel codice Python, le dimensioni totali per l'input e per l'output non possono superare i 6 GB.

## <a name="see-also"></a>Vedere anche
* [Panoramica di Analisi Data Lake di Microsoft Azure](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
* [Usare gli Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
