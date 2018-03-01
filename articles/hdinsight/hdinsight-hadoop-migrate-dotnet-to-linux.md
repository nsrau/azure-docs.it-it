---
title: Usare .NET con Hadoop MapReduce in HDInsight basato su Linux - Azure | Microsoft Docs
description: Informazioni su come usare le applicazioni .NET per lo streaming di MapReduce in HDInsight basato su Linux.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: bff53af8f5c0b74cf0f69ba474d62ecdb7e20ce1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Eseguire la migrazione di soluzioni .NET per HDInsight basato su Windows a HDInsight basato su Linux

I cluster HDInsight basati su Linux usano [Mono (https://mono-project.com)](https://mono-project.com) per eseguire le applicazioni .NET. Mono consente di usare i componenti .NET, ad esempio applicazioni MapReduce con HDInsight basato su Linux. In questo documento vengono fornite informazioni su come eseguire la migrazione di soluzioni .NET create per fare funzionare i cluster HDInsight basati su Windows con Mono in HDInsight basato su Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilità Mono con .NET

La versione Mono 4.2.1 è inclusa nella versione 3.6 di HDInsight. Per altre informazioni sulla versione Mono compresa in HDInsight, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md). Per installare una versione specifica di Mono, vedere il documento [Install or update Mono](hdinsight-hadoop-install-mono.md) (Installare o aggiornare Mono).

Per altre informazioni sulla compatibilità tra Mono e .NET, vedere il documento sulla [compatibilità Mono (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/).

> [!IMPORTANT]
> Il framework SCP.NET è compatibile con Mono. Per altre informazioni sull'uso di SCP.NET con Mono, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite gli strumenti Hadoop per Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Analisi di portabilità automatizzata

[.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) può essere usato per generare un rapporto di incompatibilità tra l'applicazione e Mono. Per configurare l'analizzatore per il controllo dell'applicazione per la portabilità Mono, usare la procedura seguente:

1. Installare [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante l'istallazione, selezionare la versione di Visual Studio da usare.

2. Da Visual Studio 2015, selezionare __Analizza__ > __Portability Analyzer Settings__ (Impostazioni analizzatore portabilità) e assicurarsi che __4.5__ sia selezionato nella sezione __Mono__.

    ![4.5 selezionato nella sezione Mono per le impostazioni di analisi](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selezionare __OK__ per salvare la configurazione.

3. Selezionare __Analizza__ > __Analyze Assembly Portability__ (Analizza portabilità assembly). Selezionare l'assembly che contiene la soluzione e quindi selezionare __Apri__ per iniziare l'analisi.

4. Una volta completata l'analisi, selezionare __Analizza__ > __View analysis reports__ (Visualizza rapporti di analisi). In __Portability Analysis Results__ (Risultati analisi di portabilità) selezionare __Open report__ (Apri rapporto) per aprire un rapporto.

    ![Finestra di dialogo dei risultati dell'analizzatore di portabilità](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> L'analizzatore non può intercettare tutti i problemi con la soluzione. Un percorso di file di `c:\temp\file.txt` ad esempio viene considerato OK se Mono è in esecuzione su Windows. Lo stesso percorso non è valido in una piattaforma Linux.

## <a name="manual-portability-analysis"></a>Analisi di portabilità manuale

Eseguire un controllo manuale del codice usando le informazioni nel documento sulla [portabilità delle applicazioni (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/).

## <a name="modify-and-build"></a>Modificare e compilare

È possibile continuare a usare Visual Studio per creare soluzioni di .NET per HDInsight. Tuttavia, è necessario assicurarsi che il progetto sia configurato per usare .NET Framework 4.5.

## <a name="deploy-and-test"></a>Distribuire e testare

Dopo aver modificato la soluzione con i consigli forniti da .NET Portability Analyzer o da un'analisi manuale, è necessario eseguirne il test con HDInsight. Fare il test della soluzione in un cluster HDInsight basato su Linux può rivelare problemi meno evidenti che devono essere corretti. Si consiglia di abilitare l'accesso aggiuntivo dell'applicazione durante l'esecuzione del test.

Per altre informazioni sull'accesso ai log, vedere i documenti seguenti:

* [Accedere ai log delle applicazioni YARN in HDInsight basato su Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Passaggi successivi

* [Use C# with MapReduce on HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md) (Usare C# con MapReduce in HDInsight)

* [Usare le funzioni definite dall'utente C# con Hive e Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite gli strumenti Hadoop per Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md)