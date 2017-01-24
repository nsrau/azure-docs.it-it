---
title: Dati di esempio nelle tabelle Hive di Azure HDInsight | Microsoft Docs
description: Esecuzione del sotto-campionamento dei dati nelle tabelle Hive (Hadoop) di Azure HDInsight
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: hangzh;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 0f3264abf6216270aa9cdd62ce3acf8640e2375a


---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dati di esempio nelle tabelle Hive di Azure HDInsight
In questo articolo, viene descritto come eseguire il downsampling dei dati archiviati nelle tabelle Hive di Azure HDInsight usando le query Hive. Vengono trattati i tre metodi di campionamento utilizzati più frequentemente:

* Campionamento casuale uniforme
* Campionamento casuale per gruppi
* Campionamento stratificato

Il **menu** seguente contiene collegamenti ad argomenti che descrivono come campionare dati di vari ambienti di archiviazione.

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Processo di analisi scientifica dei dati per i team consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Come inviare query Hive
Le query Hive possono essere inviate dalla console della riga di comando di Hadoop nel nodo head del cluster Hadoop. Per effettuare questa operazione, accedere al nodo head del cluster Hadoop, aprire la console della riga di comando e inviare le query Hive da tale posizione. Per istruzioni su come inviare le query Hive nella console della riga di comando di Hadoop, vedere [Come inviare le query Hive](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="a-nameuniforma-uniform-random-sampling"></a><a name="uniform"></a> Campionamento casuale uniforme
Nel campionamento casuale uniforme tutte le righe del set di dati hanno la stessa possibilità di essere sottoposte a campionamento. Tale metodo può essere implementato aggiungendo un ulteriore campo casuale () al set di dati relativo alla query "select" interna e a quella "select" esterna che condizionano il campo casuale.

Di seguito è fornito un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

In questo caso, `<sample rate, 0-1>` indica la proporzione di record che gli utenti desiderano campionare.

## <a name="a-namegroupa-random-sampling-by-groups"></a><a name="group"></a> Campionamento casuale per gruppi
Quando si esegue il campionamento dei dati di categoria, è possibile scegliere di includere o di escludere tutte le istanze di un determinato valore di una variabile di categoria. Questo è il significato di "campionamento per gruppi".
Ad esempio, se si dispone di variabile di categoria "State" con valori quali NY, MA, CA, NJ, PA e così via, è possibile che l'utente desideri che i record dello stesso stato siano sempre visualizzati insieme, che siano campionati o meno.

Di seguito è presentata una query di esempio che consente di eseguire il campionamento per gruppi:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="a-namestratifiedastratified-sampling"></a><a name="stratified"></a> Campionamento stratificato
Il campionamento casuale è stratificato rispetto a una variabile di categoria, nel caso in cui i campioni ottenuti presentino, per quella categoria, valori di proporzione equivalente a quelli del popolamento padre dal quale sono stati ottenuti i campioni. Usando lo stesso esempio precedente, si supponga che i dati dispongano di un popolamento secondario in base allo stato. Ad esempio, NJ dispone di 100 osservazioni, NY dispone di 60 osservazioni e WA di 300 osservazioni. Se si specifica che la proporzione del campionamento stratificato sia pari a 0,5, il campione ottenuto deve disporre all'incirca di 50, 30 e 150 osservazioni per NJ, NY e WA

Di seguito è fornito un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Per informazioni su metodi di campionamento più avanzati disponibili in Hive, vedere [Campionamento di LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).




<!--HONumber=Dec16_HO3-->


