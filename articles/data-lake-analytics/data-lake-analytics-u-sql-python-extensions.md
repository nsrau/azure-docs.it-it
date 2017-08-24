---
title: Estendere gli script U-SQL con Python in Azure Data Lake Analytics | Documentazione Microsoft
description: Informazioni su come eseguire il codice Python negli script U-SQL
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 6f3477b67b27a30e6b69f6015e9063bfa27834f7
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="tutorial-get-started-with-extending-u-sql-with-python"></a>Esercitazione: Introduzione all'estensione di U-SQL con Python

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
* [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)


