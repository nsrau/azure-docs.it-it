---
title: Eseguire programmi MapReduce personalizzati - Azure HDInsight | Microsoft Docs
description: Informazioni su quando e come eseguire programmi MapReduce personalizzati in HDInsight.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 8e65c946d2cfcc830a1b9fa59b3f7886857f4f7d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="run-custom-mapreduce-programs"></a>Eseguire programmi MapReduce personalizzati

I sistemi Big Data basati su Hadoop, come HDInsight, consentono di elaborare i dati servendosi di una vasta gamma di strumenti e tecnologie. La tabella seguente descrive i principali vantaggi di ognuno di essi, accompagnati da alcune considerazioni.

| Meccanismo di query | Vantaggi | Considerazioni |
| --- | --- | --- |
| **Hive tramite HiveQL** | <ul><li>Soluzione eccellente per l'elaborazione batch e l'analisi di grandi quantità di dati non modificabili, per il riepilogo dei dati e per l'esecuzione di query su richiesta. Usa una sintassi familiare simile a quella di SQL.</li><li>Può essere usato per produrre tabelle permanenti di dati facilmente partizionabili e indicizzabili.</li><li>È possibile creare più viste e tabelle esterne sugli stessi dati.</li><li>Supporta un'implementazione semplice di data warehouse che offre massicce funzionalità di scalabilità orizzontale e tolleranza di errore per l'elaborazione e l'archiviazione dei dati.</li></ul> | <ul><li>Richiede che i dati di origine abbiano almeno una qualche struttura identificabile.</li><li>Non è adatto per le query in tempo reale e gli aggiornamenti a livello di riga. È consigliabile per processi batch su set di dati di grandi dimensioni.</li><li>Potrebbe non riuscire a eseguire alcuni tipi di attività di elaborazione complesse.</li></ul> |
| **Pig tramite Pig Latin** | <ul><li>Soluzione eccellente per modificare i dati in set, unire e filtrare set di dati, applicare funzioni a record o gruppi di record e per ristrutturare i dati tramite la definizione di colonne, il raggruppamento di valori o la conversione di colonne in righe.</li><li>Può usare un approccio basato sul flusso di lavoro sotto forma di sequenza di operazioni sui dati.</li></ul> | <ul><li>Gli utenti di SQL potrebbero trovare Pig Latin meno familiare e più difficile da usare rispetto a HiveQL.</li><li>L'output predefinito è in genere un file di testo e quindi può essere più difficile da usare con strumenti di visualizzazione quali Excel. In genere sull'output viene aggiunta una tabella Hive.</li></ul> |
| **Componenti Map/Reduce personalizzati** | <ul><li>Offre il controllo completo sulle fasi e l'esecuzione dei componenti Map e Reduce.</li><li>Consente di ottimizzare le query in modo da ottenere prestazioni ottimali dal cluster oppure per ridurre al minimo il carico sul server e sulla rete.</li><li>I componenti possono essere scritti in una vasta gamma di linguaggi noti.</li></ul> | <ul><li>Presenta maggiori difficoltà d'uso rispetto a Pig o Hive perché è necessario creare i propri componenti Map e Reduce personalizzati.</li><li>I processi che richiedono il join di set di dati sono più difficilmente implementabili.</li><li>Anche se sono disponibili framework di test, il debug del codice è più complesso rispetto a una normale applicazione perché il codice viene eseguito come processo batch sotto il controllo del pianificatore di processi di Hadoop.</li></ul> |
| **HCatalog** | <ul><li>Estrae dal percorso i dettagli relativi all'archiviazione. In tal modo l'amministrazione risulta più agevole e gli utenti non devono più sapere dove sono archiviati i dati.</li><li>Abilita la notifica degli eventi, ad esempio relativi alla disponibilità dei dati, consentendo ad altri strumenti, come Oozie, di rilevare quando si sono verificate le operazioni.</li><li>Espone una visualizzazione relazionale dei dati, incluso il partizionamento per chiave, facilitando l'accesso ai dati.</li></ul> | <ul><li>Supporta i formati di file RCFile, testo CSV, testo JSON, SequenceFile e ORC per impostazione predefinita, ma potrebbe essere necessario scrivere un SerDe personalizzato per altri formati.</li><li>HCatalog non è thread-safe.</li><li>Quando si usa il caricatore HCatalog in script Pig, sono previste alcune limitazioni relative ai tipi di dati per le colonne. Per altre informazioni, vedere [HCatLoader Data Types](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) (Tipi di dati HCatLoader) nella documentazione di Apache HCatalog.</li></ul> |

In genere, si usa l'approccio più semplice tra quelli descritti in grado di fornire i risultati richiesti. Ad esempio, si potrebbe riuscire a ottenere questi risultati usando solo Hive, ma per scenari più complessi potrebbe essere necessario usare Pig o anche scrivere i propri componenti Map e Reduce. È anche possibile decidere, dopo aver sperimentato Hive o Pig, che sono i componenti Map e Reduce personalizzati a offrire le prestazioni migliori, in quanto consentono di regolare e ottimizzare l'elaborazione.

## <a name="custom-mapreduce-components"></a>Componenti Map/Reduce personalizzati

Il codice Map/Reduce è costituito da due funzioni separate implementate come componenti **map** e **reduce**. Il componente **map** viene eseguito in parallelo in più nodi del cluster. Ogni nodo applica il mapping al proprio subset di dati. Il componente **reduce** collaziona e riepiloga i risultati di tutte le funzioni di mapping. Per altre informazioni su questi due componenti, vedere [Usare MapReduce in Hadoop su HDInsight](hdinsight-use-mapreduce.md).

Nella maggior parte degli scenari di elaborazione di HDInsight risulta più semplice ed efficace usare un'astrazione di alto livello, ad esempio Pig o Hive. È anche possibile creare componenti Map e Reduce personalizzati da usare in script Hive per eseguire operazioni di elaborazione più sofisticate.

I componenti Map/Reduce personalizzati sono in genere scritti in Java. Hadoop offre un'interfaccia di streaming che consente anche di usare componenti sviluppati in altri linguaggi come C#, F #, Visual Basic, Python e JavaScript.

* Per la procedura relativa allo sviluppo di programmi MapReduce Java personalizzati, vedere [Sviluppare programmi Java MapReduce per Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Per un esempio di utilizzo di Python, vedere [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight](apache-hadoop-streaming-python.md).

Provare a creare i componenti Map e Reduce personalizzati per le condizioni seguenti:

* Si devono elaborare dati non completamente strutturati analizzando i dati e usando logica personalizzata per ottenere da essi informazioni strutturate.
* Si vogliono eseguire attività complesse che sono difficili o impossibili da esprimere in Pig o Hive senza dover creare una funzione definita dall'utente. Ad esempio, potrebbe essere necessario usare un servizio di geocodifica esterno per convertire in nomi di località geografiche le coordinate di latitudine e longitudine o gli indirizzi IP presenti nei dati di origine.
* Si vuole riutilizzare il codice .NET, Python o JavaScript esistente in componenti Map/Reduce tramite l'interfaccia di streaming di Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Caricare ed eseguire il programma MapReduce personalizzato

I programmi MapReduce più comuni sono scritti in Java e compilati in un file JAR.

1. Dopo avere sviluppato, compilato e testato il programma MapReduce, usare il comando `scp` per caricare il file JAR nel nodo head.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Sostituire **USERNAME** con l'account utente SSH del cluster. Sostituire **CLUSTERNAME** con il nome del cluster. Se l'account SSH è protetto da una password, viene richiesto di immetterla. Se è stata usato un certificato, può essere necessario usare il parametro `-i` per specificare il file della chiave privata.

2. Connettersi al cluster tramite [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Dalla sessione SSH eseguire il programma MapReduce tramite YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Questo comando invia il processo MapReduce a YARN. Il file di input è `/example/data/sample.log` e la directory di output è `/example/data/logoutput`. Il file di input e gli eventuali file di output saranno archiviati nella risorsa di archiviazione predefinita del cluster.

## <a name="next-steps"></a>Passaggi successivi

* [Usare C# con lo streaming di MapReduce su Hadoop in HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Sviluppare programmi Java MapReduce per Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight](apache-hadoop-streaming-python.md)
* [Usare Azure Toolkit for Eclipse per creare applicazioni Spark per un cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Usare le funzioni definite dall'utente di Python (UDF) con Hive e Pig in HDInsight](python-udf-hdinsight.md)
