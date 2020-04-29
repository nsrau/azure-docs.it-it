---
title: Usare .NET per Apache Spark con Azure sinapsi Analytics
description: Informazioni sull'uso di .NET e Apache Spark per l'elaborazione batch, lo streaming in tempo reale, l'apprendimento automatico e la scrittura di query ad hoc nei notebook di analisi delle sinapsi di Azure.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430513"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Usare .NET per Apache Spark con Azure sinapsi Analytics

[.NET per Apache Spark](https://dot.net/spark) è il supporto .NET gratuito, open source e multipiattaforma per Spark. .NET per Apache Spark fornisce associazioni .NET per Spark che consentono di accedere alle API Spark tramite C# e F #. Con .NET per Apache Spark è possibile scrivere ed eseguire funzioni definite dall'utente per Spark usando .NET. Le API .NET per Spark consentono di accedere a tutti gli aspetti di Spark che consentono di analizzare i dati, tra cui Spark SQL e il flusso strutturato.

È possibile analizzare i dati con .NET per Apache Spark tramite le definizioni dei processi batch Spark o con i notebook di Azure sinapsi Analytics interattivi. Questo articolo illustra come usare .NET per Apache Spark con la sinapsi di Azure con entrambe le tecniche. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Inviare processi batch con la definizione del processo Spark

Vedere l'esercitazione per informazioni su come usare l'analisi delle sinapsi di Azure per [creare Apache Spark definizioni dei processi per i pool della sinapsi Spark](apache-spark-job-definitions.md). Se l'app non è stata compilata per l'invio a sinapsi di Azure, seguire questa procedura.

1. Eseguire i comandi seguenti per pubblicare l'app. Assicurarsi di sostituire *mySparkApp* con il percorso dell'app.

   **In Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **In Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark nei notebook di Azure sinapsi Analytics

Quando si crea un nuovo notebook, si sceglie un kernel del linguaggio che si desidera esprimere alla logica di business. È disponibile il supporto del kernel per diversi linguaggi, tra cui C#.

Per usare .NET per Apache Spark nel notebook di Azure sinapsi Analytics, selezionare **.NET Spark (C#)** come kernel e aggiungere il notebook a un pool Spark esistente.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark nei notebook di Azure sinapsi Analytics 

I notebook sono un'ottima opzione per la realizzazione di prototipi di .NET per Apache Spark pipeline e scenari. È possibile iniziare a usare, comprendere, filtrare, visualizzare e visualizzare i dati in modo rapido ed efficiente. Gli ingegneri dei dati, i data scientist, i Business Analyst e i tecnici di Machine Learning sono tutti in grado di collaborare in un documento condiviso e interattivo. È possibile visualizzare i risultati immediati dall'esplorazione dei dati e visualizzare i dati nello stesso notebook.

### <a name="how-to-use-notebooks"></a>Come usare i notebook

Quando si crea un nuovo notebook, si sceglie un kernel del linguaggio che si desidera esprimere alla logica di business. È disponibile il supporto del kernel per diversi linguaggi, tra cui C#. 

Per usare .NET per Apache Spark nel notebook di Azure sinapsi Analytics, selezionare **.NET Spark (C#)** come kernel e aggiungere il notebook a un pool Spark esistente. 

Il notebook di Spark per .NET si basa sulle esperienze interattive di .NET e offre esperienze interattive in C# con la possibilità di usare .NET per Spark con la variabile `spark` di sessione Spark già predefinita.

### <a name="sparknet-c-kernel-features"></a>Funzionalità del kernel C# di Spark.NET

Le funzionalità seguenti sono disponibili quando si usa .NET per Apache Spark nel notebook di analisi delle sinapsi di Azure:

* HTML dichiarativo: genera l'output dalle celle usando la sintassi HTML, ad esempio intestazioni, elenchi puntati e persino la visualizzazione di immagini.
* Semplici istruzioni C#, ad esempio assegnazioni, stampa nella console, generazione di eccezioni e così via.
* Blocchi di codice C# a più righe, ad esempio istruzioni If, cicli foreach, definizioni di classi e così via.
* Accesso alla libreria C# standard, ad esempio System, LINQ, Enumerable e così via.
* Supporto per le [funzionalità del linguaggio C# 8,0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* "Spark" come variabile predefinita per consentire l'accesso alla sessione di Apache Spark.
* Supporto per la definizione di [funzioni definite dall'utente .NET che possono essere eseguite all'interno Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Supporto per la visualizzazione dell'output dei processi Spark usando grafici diversi, ad esempio linee, barre o istogrammi, e layout (ad esempio singoli, sovrapposti e così via) usando la `XPlot.Plotly` libreria.
* Possibilità di includere pacchetti NuGet nel notebook C#.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interattivo](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)