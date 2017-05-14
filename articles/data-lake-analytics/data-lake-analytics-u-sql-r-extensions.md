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
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Esercitazione: Introduzione all'estensione di U-SQL con R

L'esempio seguente illustra i passaggi di base per la distribuzione del codice R:
* Usare l'istruzione REFERENCE ASSEMBLY per abilitare le estensioni R per lo script U-SQL.
* Uso dell'operazione REDUCE per partizionare i dati di input in una chiave.
* Le estensioni R per U-SQL includono un riduttore predefinito (Extension.R.Reducer) che esegue il codice R in ogni vertice assegnato al riduttore. 
* Uso dei frame di dati denominati dedicati chiamati rispettivamente inputFromUSQL e outputToUSQL per passare dati tra USQL e R. I nomi di input e di output dell'identificatore DataFrame sono fissi (vale a dire, gli utenti non possono modificare i nomi predefiniti di input e di output degli identificatori DataFrame).


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

## <a name="how-r-integrates-with-u-sql"></a>Come R si integra con U-SQL

### <a name="datatypes"></a>Tipi di dati
* Le colonne di tipo stringa e numeriche di U-SQL vengono convertite così come sono tra DataFrame R e U-SQL [tipi supportati: double, string, bool, integer, byte].
* Il fattore datatype non è supportato in U-SQL.
* byte[] deve essere serializzato come una stringa con codifica Base64.
* Le stringhe U-SQL possono essere convertite in fattori nel codice R, una volta che U SQL crea il frame di dati R di input o impostando il parametro riduttore stringsAsFactors: true.

### <a name="schemas"></a>Schemi
* I set di dati di U-SQL non possono avere nomi di colonna duplicati.
* I nomi di colonna dei set di dati di U-SQL devono essere stringhe.
* I nomi di colonna devono essere identici in U-SQL e negli script R.
* Le colonne di sola lettura non possono fare parte del frame di dati di output. Poiché le colonne di sola lettura vengono inserite automaticamente nella tabella U-SQL se fanno parte dello schema di output degli oggetti UDO.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)

