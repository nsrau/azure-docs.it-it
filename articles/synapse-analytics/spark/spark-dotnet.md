---
title: Usare .NET per Apache Spark con Azure Synapse Analytics
description: Informazioni sull'uso di .NET e Apache Spark per l'elaborazione batch, lo streaming in tempo reale, l'apprendimento automatico e la scrittura di query ad hoc in notebook di Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 3882352c7e1d484818a58d7bd4410cbd66bd6637
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587800"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Usare .NET per Apache Spark con Azure Synapse Analytics

[.NET per Apache Spark](https://dot.net/spark) corrisponde al supporto .NET gratuito, open-source e multipiattaforma per Spark. .NET per Apache Spark fornisce le associazioni .NET per Spark che consentono di accedere alle API Spark tramite C# e F#. Con .NET per Apache Spark è possibile scrivere ed eseguire funzioni definite dall'utente per Spark usando .NET. Con le API .NET per Spark è possibile accedere a tutti gli aspetti di Spark che consentono di analizzare i dati, tra cui Spark SQL e il flusso strutturato.

Per analizzare i dati con .NET per Apache Spark, è possibile usare definizioni di processo batch Spark o notebook interattivi di Azure Synapse Analytics. Questo articolo illustra come usare .NET per Apache Spark con Azure Synapse in entrambi i modi.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Inviare processi batch con la definizione di processo Spark

Vedere l'esercitazione per informazioni su come usare Azure Synapse Analytics per [creare definizioni di processi Apache Spark per i pool di Synapse Spark](apache-spark-job-definitions.md). Se non è stato creato il pacchetto dell'app da inviare ad Azure Synapse, seguire questa procedura.

1. Eseguire i comandi seguenti per pubblicare l'app. Assicurarsi di sostituire *mySparkApp* con il percorso dell'app.

   **In Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **In Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark in notebook di Azure Synapse Analytics

Quando si crea un nuovo notebook, è necessario scegliere un kernel del linguaggio con cui esprimere la logica di business. Il supporto del kernel è disponibile per diversi linguaggi, tra cui C#.

Per usare .NET per Apache Spark nel notebook di Azure Synapse Analytics, selezionare **.NET Spark (C#)** come kernel e collegare il notebook a un pool di Spark esistente.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark in notebook di Azure Synapse Analytics 

I notebook costituiscono un valida opzione per la realizzazione di prototipi di pipeline e scenari di .NET per Apache Spark. È possibile iniziare a usare, comprendere, filtrare e visualizzare i dati in modo rapido ed efficiente. Data engineer, data scientist, business analyst e machine learning engineer possono collaborare lavorando tutti a un documento condiviso e interattivo. È possibile accedere ai risultati immediati dall'esplorazione dei dati e visualizzare i dati nello stesso notebook.

### <a name="how-to-use-notebooks"></a>Come usare i notebook

Quando si crea un nuovo notebook, è necessario scegliere un kernel del linguaggio con cui esprimere la logica di business. Il supporto del kernel è disponibile per diversi linguaggi, tra cui C#.

Per usare .NET per Apache Spark nel notebook di Azure Synapse Analytics, selezionare **.NET Spark (C#)** come kernel e collegare il notebook a un pool di Spark esistente.

Il notebook di .NET per Spark si basa su esperienze interattive .NET e offre esperienze interattive C# con la possibilità di usare .NET per Spark con la variabile di sessione `spark` di Spark già predefinita.

### <a name="sparknet-c-kernel-features"></a>Funzionalità del kernel C# di Spark.NET

Le funzionalità seguenti sono disponibili quando si usa .NET per Apache Spark nel notebook di Azure Synapse Analytics:

* HTML dichiarativo: genera l'output dalle celle usando la sintassi HTML, ad esempio intestazioni, elenchi puntati e persino visualizzazione di immagini.
* Istruzioni C# semplici, ad esempio assegnazioni, stampa alla console, generazione di eccezioni e così via.
* Blocchi di codice C# su più righe, ad esempio istruzioni if, cicli foreach, definizioni di classi e così via.
* Accesso alla libreria standard C#, ad esempio System, LINQ, tipi enumerabili e così via.
* Supporto per le [funzionalità del linguaggio C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'spark' come variabile predefinita per consentire l'accesso alla sessione di Apache Spark.
* Supporto per la definizione di [funzioni definite dall'utente .NET che possono essere eseguite all'interno di Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Supporto per la visualizzazione dell'output dei processi Spark con grafici (ad esempio a linee, a barre o istogrammi) e layout (ad esempio singoli, sovrapposti e così via) diversi usando la libreria `XPlot.Plotly`.
* Possibilità di includere i pacchetti NuGet nel notebook C#.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)