<properties linkid="manage-services-hdinsight-run-Hadoop samples" urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Run the Hadoop samples in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Eseguire gli esempi di Hadoop in HDInsight

Sono disponibili alcuni esempi utili per iniziare a eseguire processi MapReduce nei cluster Hadoop usando Azure HDInsight. Questi esempi sono disponibili in ogni cluster creato e gestito da HDInsight. L'esecuzione di questi esempi permetterà di familiarizzare con l'uso dei cmdlet di Azure PowerShell per HDInsight per l'esecuzione di processi nei cluster Hadoop.

È inoltre possibile eseguire programmi MapReduce a livello di codice da un'applicazione utilizzando l'API Microsoft .NET per HDInsight. Per altre informazioni sull'utilizzo di API per HDInsight, vedere, [Inviare processi Hadoop a livello di codice][].

Sul Web sono disponibili molti altri documenti aggiuntivi per le tecnologie relative a Hadoop, ad esempio la programmazione e lo streaming MapReduce basati su Java, oltre alla documentazione sull'utilizzo dei cmdlet negli script PowerShell. Per altre informazioni su queste risorse, vedere la sezione finale **Risorse per HDInsight** dell'argomento [Introduzione ad Azure HDInsight][].

**Informazioni sugli esempi**

Questi esempi consentono di imparare rapidamente a distribuire processi Hadoop e offrono un banco di prova estendibile per i concetti le procedure di scripting utilizzate dal servizio. Offrono esempi di attività comuni, ad esempio la creazione e l'importazione di set di dati di diverse dimensioni, l'esecuzione di processi e la composizione di processi in sequenza e l'esame dei risultati dei processi. La dimensione dei set di dati utilizzati può variare, consentendo di osservare gli effetti delle diverse dimensioni dei set di dati sulle prestazioni dei processi.

**Prerequisiti**:

-   È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese][].

-   È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight][]

-   È necessario che Azure PowerShell sia installato e configurato per l'utilizzo con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell][].

## Esempi

In HDInsight sono disponibili gli esempi seguenti.

-   [**Esempio di calcolo del Pi greco**][] In questa esercitazione viene illustrato come eseguire un programma MapReduce con HDInsight che utilizza un metodo statistico (simile al metodo Monte Carlo) per calcolare il valore di Pi greco.
-   [**Esempio WordCount**][] In questa esercitazione viene illustrato come utilizzare un cluster HDInsight per eseguire un programma MapReduce che conta le occorrenze delle parole in un file di testo.
-   [**Esempio Graysort da 10 GB**][] In questa esercitazione viene illustrato come eseguire un ordinamento GraySort di utilizzo generale in un file da 10 GB mediante HDInsight. I processi da eseguire sono tre: Teragen per generare i dati, Terasort per ordinare i dati e Teravalidate per confermare che i dati siano stati ordinati correttamente.
-   [**Esempio di flusso C#**][] In questa esercitazione viene illustrato come utilizzare C# per scrivere un programma MapReduce che utilizza l'interfaccia di flusso Hadoop.

## Come eseguire gli esempi

Gli esempi possono essere eseguiti mediante Azure PowerShell. Per le istruzioni relative a questa operazione, fare clic sopra sui collegamenti alle pagine di ogni esempio.

## Passaggi successivi

In questo articolo e negli articoli su ciascuno degli esempi, è stato illustrato come eseguire gli esempi inclusi nei cluster HDInsight mediante Azure PowerShell. Per le esercitazioni sull'utilizzo di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

-   [Introduzione all'utilizzo del servizio Azure HDInsight][]
-   [Usare Pig con HDInsight][]
-   [Usare Hive con HDInsight][]
-   [Inviare processi Hadoop a livello di codice][]
-   [Documentazione di Azure HDInsight SDK][]
-   [Debug di HDInsight: messaggi di errore][]

  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Introduzione ad Azure HDInsight]: ../hdinsight-introduction/
  [Abbonamento di prova gratuito di un mese]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Installazione e configurazione di Azure PowerShell]: ../install-configure-powershell/
  [**Esempio di calcolo del Pi greco**]: ../hdinsight-sample-pi-estimator/
  [**Esempio WordCount**]: ../hdinsight-sample-wordcount/
  [**Esempio Graysort da 10 GB**]: ../hdinsight-sample-10gb-graysort/
  [**Esempio di flusso C#**]: ../hdinsight-sample-csharp-streaming/
  [Introduzione all'utilizzo del servizio Azure HDInsight]: ../hdinsight-get-started/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Documentazione di Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
  [Debug di HDInsight: messaggi di errore]: ../hdinsight-debug-jobs/
