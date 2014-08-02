<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Run HDInsight Samples" pageTitle="Run the HDInsight samples | Azure" metaKeywords="" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the HDInsight samples" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Esecuzione degli esempi relativi a HDInsight
============================================

È disponibile un set di esempi per imparare a eseguire facilmente le attività iniziali di Azure HDInsight. Questi esempi sono disponibili in ogni cluster HDInsight creato. L'esecuzione di questi esempi consentirà di familiarizzare con i cmdlet HDInsight di Azure PowerShell.

È inoltre possibile eseguire programmi MapReduce a livello di codice da un'applicazione utilizzando l'API Microsoft .NET per HDInsight. Per ulteriori informazioni sull'utilizzo di API per HDInsight, vedere [Invio di processi Hadoop a livello di codice](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/).

Sul Web sono disponibili molti altri documenti aggiuntivi per le tecnologie relative a Hadoop, ad esempio la programmazione e lo streaming MapReduce basati su Java, oltre alla documentazione sull'utilizzo dei cmdlet negli script PowerShell. Per ulteriori informazioni su queste risorse, vedere la sezione finale **Risorse per HDInsight** dell'argomento [Introduzione ad Azure HDInsight](/en-us/manage/services/hdinsight/introduction-hdinsight/).

**Informazioni sugli esempi**

Questi esempi consentono di imparare rapidamente a distribuire processi Hadoop e offrono un banco di prova estendibile per i concetti le procedure di scripting utilizzate dal servizio. Offrono esempi di attività comuni, ad esempio la creazione e l'importazione di set di dati di diverse dimensioni, l'esecuzione di processi e la composizione di processi in sequenza e l'esame dei risultati dei processi. La dimensione dei set di dati utilizzati può variare, consentendo di osservare gli effetti delle diverse dimensioni dei set di dati sulle prestazioni dei processi.

**Prerequisiti**:

-   È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   È necessario che Azure PowerShell sia installato e configurato per l'utilizzo con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Esempi
------

In HDInsight sono disponibili gli esempi seguenti.

-   [**Esempio di calcolo del Pi greco**](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/) In questa esercitazione viene illustrato come eseguire un programma MapReduce con HDInsight che utilizza un metodo statistico (simile al metodo Monte Carlo) per calcolare il valore di Pi greco.
-   [**Esempio WordCount**](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/) In questa esercitazione viene illustrato come utilizzare un cluster HDInsight per eseguire un programma MapReduce che conta le occorrenze delle parole in un file di testo.
-   [**Esempio Graysort da 10 GB**](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/) In questa esercitazione viene illustrato come eseguire un ordinamento GraySort di utilizzo generale in un file da 10 GB mediante HDInsight. I processi da eseguire sono tre: Teragen per generare i dati, Terasort per ordinare i dati e Teravalidate per confermare che i dati siano stati ordinati correttamente.
-   [**Esempio di flusso C\#**](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/) In questa esercitazione viene illustrato come utilizzare C\# per scrivere un programma MapReduce che utilizza l'interfaccia di flusso Hadoop.

Come eseguire gli esempi
------------------------

Gli esempi possono essere eseguiti mediante Azure PowerShell. Per le istruzioni relative a questa operazione, fare clic sopra sui collegamenti alle pagine di ogni esempio.

Passaggi successivi
-------------------

In questo articolo e negli articoli su ciascuno degli esempi, è stato illustrato come eseguire gli esempi inclusi nei cluster HDInsight mediante Azure PowerShell. Per le esercitazioni sull'utilizzo di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

-   [Introduzione all'utilizzo del servizio Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Invio di processi Hadoop a livello di codice](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
-   [Debug di HDInsight: messaggi di errore](/en-us/manage/services/hdinsight/debug-error-messages/)

