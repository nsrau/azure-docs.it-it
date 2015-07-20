<properties
	pageTitle="Eseguire gli esempi di Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come iniziare a usare il servizio Azure HDInsight con gli esempi forniti. Usare script PowerShell che eseguono programmi MapReduce su cluster di dati."
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015"
	ms.author="bradsev"/>




#Eseguire gli esempi di Hadoop in HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Sono disponibili alcuni esempi utili per iniziare a eseguire processi MapReduce nei cluster Hadoop usando Azure HDInsight. Questi esempi sono disponibili in ogni cluster creato e gestito da HDInsight. L'esecuzione di questi esempi permetterà di acquisire familiarità con l'uso dei cmdlet di Azure PowerShell per l'esecuzione di processi nei cluster Hadoop.

È possibile eseguire programmi MapReduce anche a livello di codice da un'applicazione usando l'API Microsoft .NET per HDInsight. Per altre informazioni sull'uso di API per HDInsight per l'invio di processi, vedere [Inviare processi Hadoop in HDInsight][hdinsight-submit-jobs].

Sul Web sono disponibili molti altri documenti per le tecnologie relative a Hadoop, ad esempio il flusso e la programmazione MapReduce basati su Java, oltre alla documentazione sui cmdlet usati negli script di Windows PowerShell. Per altre informazioni su queste risorse, vedere la sezione finale **Risorse per HDInsight** dell'argomento [Introduzione ad Azure HDInsight][hdinsight-introduction].

**Informazioni sugli esempi**

<p>Questi esempi consentono di imparare rapidamente a distribuire processi Hadoop e offrono un banco di prova estendibile per comprendere i concetti e gestire le procedure di scripting del servizio. Vengono forniti esempi di attività comuni, come la creazione e l'importazione di set di dati di varie dimensioni, l'esecuzione di processi, la composizione di processi in sequenza e l'esame dei risultati dei processi. La dimensione dei set di dati usati può variare. È così possibile osservare gli effetti delle diverse dimensioni dei set di dati sulle prestazioni dei processi.</p>


**Prerequisiti**:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un cluster HDInsight**. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md).
- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Esempi ##

In HDInsight sono disponibili gli esempi seguenti:

- [**Esempio di calcolo del Pi greco in Hadoop**][hdinsight-sample-pi-estimator]: mostra come eseguire un programma MapReduce con HDInsight che usa un metodo statistico (simile al metodo Monte Carlo) per calcolare il valore di Pi greco.
- [**Esempio di conteggio delle parole con MapReduce in un cluster Hadoop**][hdinsight-sample-wordcount]: mostra come usare un cluster HDInsight per eseguire un programma MapReduce che conta le occorrenze delle parole in un file di testo.
- [**Esempio Graysort di Hadoop da 10 GB**][hdinsight-sample-10gb-graysort]: mostra come eseguire un ordinamento GraySort generico in un file da 10 GB usando HDInsight. I processi da eseguire sono tre: Teragen per generare i dati, Terasort per ordinare i dati e Teravalidate per verificare che i dati siano stati ordinati correttamente.
- [**Esempio C# di conteggio delle parole di un flusso con MapReduce in Hadoop**][hdinsight-sample-csharp-streaming]: mostra come usare C# per scrivere un programma MapReduce che usa l'interfaccia di flusso Hadoop.


## Come eseguire gli esempi ##

Gli esempi possono essere eseguiti mediante Azure PowerShell. Le istruzioni su come eseguire questa operazione vengono fornite per ciascuno degli esempi.

##Passaggi successivi ##

In questo articolo e negli articoli relativi ai singoli esempi, si è appreso come eseguire gli esempi inclusi nei cluster HDInsight usando Azure PowerShell. Per le esercitazioni sull'uso di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]
* [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Inviare processi Hadoop in HDInsight][hdinsight-submit-jobs]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Eseguire il debug di Hadoop in HDInsight: messaggi di errore][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO2-->