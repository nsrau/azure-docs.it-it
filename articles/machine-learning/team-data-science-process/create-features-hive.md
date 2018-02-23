---
title: "Creare funzionalità per i dati in un cluster Hadoop mediante le query Hive | Documentazione Microsoft"
description: "Esempi di query Hive che generano funzionalità nei dati archiviati in un cluster HDInsight Hadoop di Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 91ea23b732f520b02af7e9a9dd77ee62190a520c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Creare funzionalità per i dati in un cluster Hadoop con query Hive
Questo documento illustra come creare funzionalità per i dati archiviati in un cluster Hadoop di Azure HDInsight tramite query Hive. Tali query Hive usano le funzioni definite dall'utente di Hive incorporate, gli script per i quali sono fornite.

Le operazioni necessarie per creare le funzionalità possono richiedere molta memoria. Le prestazioni delle query Hive diventano più importanti in questi casi e possono essere migliorate, ottimizzando determinati parametri. L'ottimizzazione di questi parametri è descritta nella sezione finale.

Nell'[archivio GitHub](http://chriswhong.com/open-data/foil_nyc_taxi/) sono disponibili anche alcuni esempi di query specifiche per gli scenari relativi ai [dati dei tragitti dei taxi di NYC](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tali query dispongono già di un determinato schema dei dati e possono essere inviate e usate immediatamente. Nella parte finale del documento sono descritti anche i parametri che gli utenti possono impostare per migliorare le prestazioni delle query Hive.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare funzionalità per dati in diversi ambienti. Questa attività è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight.  Per istruzioni, vedere [Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](customize-hadoop-cluster.md).
* Caricato i dati nelle tabelle Hive dei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata eseguita, attenersi alle istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](move-hive-tables.md) per caricare prima i dati nelle tabelle Hive.
* Abilitato l'accesso remoto al cluster. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Creazione di funzionalità
In questa sezione vengono descritti vari esempi dei modi in cui è possibile generare funzionalità mediante le query Hive. Dopo aver creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente oppure creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che quindi può essere unita alla tabella originale. Di seguito ci sono gli esempi presentati:

1. [Creazione di funzionalità basata sulla frequenza](#hive-frequencyfeature)
2. [Rischi di variabili di categoria nella classificazione binaria](#hive-riskfeature)
3. [Estrazione delle funzionalità dal campo Datetime](#hive-datefeatures)
4. [Estrazione delle funzionalità dal campo Text](#hive-textfeatures)
5. [Calcolo della distanza tra le coordinate GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Creazione di funzionalità basata sulla frequenza
Spesso, può essere utile calcolare le frequenze relative ai livelli di una variabile di categoria o le frequenze di alcune combinazioni di livelli di più variabili di categoria. Per calcolare tali frequenze, gli utenti possono usare lo script seguente:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Rischi di variabili di categoria nella classificazione binaria
Nella classificazione binaria, se i modelli usati accettano soltanto funzionalità numeriche, è necessario convertire le variabili di categoria non numeriche in funzionalità numeriche. È possibile eseguire questa conversione sostituendo tutti i livelli non numerici con un rischio numerico. Questa sezione descrive alcune query Hive generiche che consentono di calcolare i valori di rischio (disparità di log) di una variabile di categoria.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

In questo esempio, le variabili `smooth_param1` e `smooth_param2` vengono impostate per arrotondare i valori di rischio calcolati dai dati. Rischi devono essere compresi in un intervallo di -Inf e Inf. Un rischio > 0 indica che la probabilità che la destinazione sia pari a 1 è maggiore di 0,5.

Dopo aver calcolato la tabella dei rischi, gli utenti possono assegnare i valori di rischio a una tabella unendola a quella dei rischi. La query Hive che viene aggiunta è stata descritta nella sezione precedente.

### <a name="hive-datefeatures"></a>Estrarre le funzionalità dal campo Datetime
In Hive è disponibile un set di funzioni definite dall'utente per elaborare i campi Datetime. In Hive, il formato data/ora predefinito è "aaaa-MM-gg 00:00:00" (ad esempio, "1970-01-01 12:21:32"). Questa sezione include esempi che consentono di estrarre il giorno del mese, il mese da un campo Datetime e altri esempi che consentono di convertire una stringa data/ora in formato diverso rispetto a quello predefinito da una stringa data/ora con formato predefinito.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Questa query Hive presuppone che *&#60;datetime field>* sia nel formato di data/ora predefinito.

Se un campo data/ora non è nel formato predefinito, è necessario convertire innanzitutto il campo Datetime in indicatore data/ora Unix e convertire quest'ultimo in una stringa data/ora nel formato predefinito. Quando la stringa data/ora è nel formato predefinito, è possibile applicare le funzioni data/ora incorporate e definite dall'utente al fine di estrarre le funzionalità.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

In questa query, se *&#60;datetime field>* ha il modello *03/26/2015 12:04:39*, *'&#60;pattern of the datetime field>'* deve essere `'MM/dd/yyyy HH:mm:ss'`. Per eseguire il test, gli utenti possono eseguire

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

In questa query, *hivesampletable* presenta tutti i cluster Hadoop di Azure HDInsight per impostazione predefinita quando viene effettuato il provisioning dei cluster.

### <a name="hive-textfeatures"></a>Estrarre le funzionalità dal campo Text
Quando la tabella Hive dispone di un campo di testo con una stringa di parole delimitate da spazi, la seguente query consente di estrarre la lunghezza della stringa e il numero di parole contenuto.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcolo della distanza tra le coordinate GPS
La query descritta in questa sezione può essere applicata direttamente ai dati relativi ai tragitti dei taxi di NYC. Lo scopo della query è quello di dimostrare come applicare una funzione matematica incorporata in Hive, al fine di creare funzionalità.

I campi usati in questa query sono coordinate GPS relative ai luoghi in cui si sale e scende dal taxi, denominati *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* e *dropoff\_latitude*. Le query che consentono di calcolare la distanza diretta tra questi due punti sono:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Le equazioni matematiche per calcolare la distanza tra due coordinate GPS sono riportate nel sito <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a>, creato da Peter Lapisu. In JavaScript la funzione `toRad()` è *lat_or_lon*pi/180* e consente di convertire i gradi in radianti. Qui *lat_or_lon* rappresenta la latitudine o la longitudine. Dal momento che Hive non fornisce la funzione `atan2`, ma `atan`, la funzione `atan2` viene implementata dalla funzione `atan` nella query Hive precedente in base alla definizione fornita su <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Creare un'area di lavoro](./media/create-features-hive/atan2new.png)

Nella sezione **Funzionalità integrate** del <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Wiki su Apache Hive</a> è disponibile un elenco completo delle funzioni definite e incorporate di Hive.  

## <a name="tuning"></a> Argomento avanzato: Ottimizzare i parametri Hive per migliorare la velocità delle query
Le impostazioni predefinite per i parametri del cluster Hive potrebbero non essere adatte alle query Hive e ai dati elaborati dalle query. In questa sezione vengono illustrati alcuni parametri che gli utenti possono regolare per migliorare le prestazioni delle query Hive. Gli utenti devono aggiungere la query di regolazione del parametro prima della query di elaborazione dei dati.

1. **Spazio dell'heap di Java**: per le query che comportano l'unione di set di dati di grandi dimensioni o l'elaborazione di record estesi, un errore comune è quello di **esaurire lo spazio dell'heap**. Questo errore può essere evitato mediante l'impostazione di parametri *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* sui valori desiderati. Di seguito è fornito un esempio: 
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Questo parametro consente di allocare 4 GB di memoria nello spazio dell'heap di Java e facilita l'ordinamento poiché rende disponibile maggiore quantità di memoria. Si consiglia di regolare queste allocazioni se si verificano errori di processo relativi allo spazio dell'heap.

1. **Dimensione di blocco per DFS**: questo parametro consente di impostare la quantità minima relativa all'unità di dati che il file system è in grado di memorizzare. Ad esempio, se la dimensione del blocco DFS è 128 MB, tutti i dati con dimensioni minori o uguali a 128 MB vengono archiviati in un unico blocco. Ai dati con dimensioni maggiori di 128 MB vengono assegnati blocchi aggiuntivi. 
2. Se si sceglie una dimensione di blocco limitata si causano sovraccarichi in Hadoop, dal momento che il nodo del nome deve elaborare molte più richieste al fine di rilevare il blocco che fa riferimento al file. Un'impostazione consigliata per dati con dimensioni in gigabyte (o più grandi) è:

        set dfs.block.size=128m;

2. **Ottimizzazione delle operazioni di unione in Hive**: anche se le operazioni nel framework di mapping/riduzione avvengono solitamente nella fase di riduzione, è possibile talvolta ottenere dei miglioramenti pianificando le operazioni di unione nella fase di mapping (detta anche "mapjoin"). Per indirizzare Hive a tale scopo, quando possibile, impostare:
   
       set hive.auto.convert.join=true;

3. **Specifica del numero di mapper in Hive**: mentre Hadoop consente all'utente di impostare il numero di riduttori, il numero di mapper generalmente non viene impostato dall'utente. Un espediente che offre un certo livello di controllo su questo numero consiste nello scegliere le variabili di Hadoop *mapred.min.split.size* e *mapred.max.split.size* come dimensione di ogni attività di mapping determinata da:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    In genere il valore predefinito di:
    
    - *mapred.min.split.size* è 0
    - *mapred.max.split.size* è **Long.MAX** 
    - *dfs.block.size* è 64 MB.

    Come è evidente, stabilita la dimensione dei dati, regolare i parametri "impostandoli" consente all'utente di scegliere il numero di mapper da usare.

4. Di seguito sono riportate altre **opzioni avanzate** che consentono di ottimizzare le prestazioni di Hive. Tali opzioni consentono di impostare la quantità di memoria allocata per le attività di mapping e di riduzione e possono essere utili nella modifica delle prestazioni. Tenere presente che *mapreduce.reduce.memory.mb* non può essere superiore alla dimensione della memoria fisica di ogni nodo di lavoro presente nel cluster Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

