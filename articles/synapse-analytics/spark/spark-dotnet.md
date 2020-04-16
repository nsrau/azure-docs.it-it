---
title: Usare .NET per Apache Spark con Azure Synapse AnalyticsUse .NET for Apache Spark with Azure Synapse Analytics
description: Informazioni sull'uso di .NET e Apache Spark per eseguire l'elaborazione batch, lo streaming in tempo reale, l'apprendimento automatico e la scrittura di query ad hoc nei blocchi appunti di Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430513"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Usare .NET per Apache Spark con Azure Synapse AnalyticsUse .NET for Apache Spark with Azure Synapse Analytics

[.NET per Apache Spark](https://dot.net/spark) è il supporto gratuito, open source e multipiattaforma .NET per Spark. .NET per Apache Spark fornisce le associazioni .NET per Spark che consentono di accedere alle API Spark tramite C . Con .NET per Apache Spark, è possibile scrivere ed eseguire funzioni definite dall'utente per Spark utilizzando .NET. Le API .NET per Spark consentono di accedere a tutti gli aspetti di Spark che consentono di analizzare i dati, inclusi Spark SQL e Structured Streaming.

È possibile analizzare i dati con .NET per Apache Spark tramite le definizioni dei processi batch Spark o con blocchi appunti interattivi di Azure Synapse Analytics.You can analyze data with .NET for Apache Spark through Spark batch job definitions or with interactive Azure Synapse Analytics notebooks. In questo articolo viene illustrato come usare .NET per Apache Spark con Azure Synapse usando entrambe le tecniche. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Inviare processi batch utilizzando la definizione del processo Spark

Vedere l'esercitazione per informazioni su come usare Analisi synapse di Azure per creare definizioni di [processo Apache Spark per i pool Spark di Synapse](apache-spark-job-definitions.md). Se l'app non è stata inserita nel pacchetto da inviare ad Azure Synapse, completare i passaggi seguenti.

1. Esegui i comandi seguenti per pubblicare l'app. Assicurati di sostituire *mySparkApp* con il percorso della tua app.

   **In Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Su Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark nei blocchi appunti di Azure Synapse Analytics

Quando si crea un nuovo blocco appunti, si sceglie un kernel di lingua che si desidera esprimere la logica di business. C'è il supporto del kernel per diversi linguaggi, tra cui C .

Per usare .NET per Apache Spark nel blocco appunti di Azure Synapse Analytics, seleziona **.NET Spark (C)** come kernel e collega il blocco appunti a un pool Spark esistente.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark nei blocchi appunti di Azure Synapse Analytics 

I blocchi appunti sono un'ottima opzione per la creazione di prototipi di .NET per le pipeline e gli scenari Apache Spark. È possibile iniziare a lavorare, comprendere, filtrare, visualizzare e visualizzare i dati in modo rapido ed efficiente. Ingegneri di dati, data scientist, analisti aziendali e ingegneri di apprendimento automatico sono tutti in grado di collaborare tramite un documento condiviso e interattivo. I risultati immediati dell'esplorazione dei dati vengono visualizzati e i dati possono essere visualizzati nello stesso blocco appunti.

### <a name="how-to-use-notebooks"></a>Come usare i notebook

Quando si crea un nuovo blocco appunti, si sceglie un kernel di lingua in cui si desidera esprimere la logica di business. C'è il supporto del kernel per diversi linguaggi, tra cui C . 

Per usare .NET per Apache Spark nel blocco appunti di Azure Synapse Analytics, seleziona **.NET Spark (C)** come kernel e collega il blocco appunti a un pool Spark esistente. 

Il blocco appunti di .NET Spark è basato sulle esperienze interattive di .NET e offre esperienze interattive in C, con la possibilità di usare .NET per Spark predefiniti con la variabile `spark` di sessione Spark già predefinita.

### <a name="sparknet-c-kernel-features"></a>funzionalità del kernel di Spark.NET c'è

Le funzionalità seguenti sono disponibili quando si usa .NET per Apache Spark nel blocco appunti di Azure Synapse Analytics:The following features are available when you use .NET for Apache Spark in the Azure Synapse Analytics notebook:

* HTML dichiarativo: genera l'output dalle celle usando la sintassi HTML, ad esempio intestazioni, elenchi puntati e persino la visualizzazione di immagini.
* Istruzioni semplici in C, ad esempio assegnazioni, stampa su console, generazione di eccezioni e così via.
* Blocchi di codice in più righe di C, ad esempio istruzioni if, cicli foreach, definizioni di classi e così via.
* L'accesso alla libreria standard di C, ad esempio System, LINQ, Enumerables e così via.
* Supporto per [le funzionalità del linguaggio c'è 8.0.](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* 'scintilla' come variabile predefinita per darti accesso alla tua sessione Apache Spark.
* Supporto per la definizione di [funzioni definite dall'utente .NET che possono essere eseguite all'interno di Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Supporto per la visualizzazione dell'output dei processi Spark utilizzando grafici diversi (ad esempio a linee, a barre o `XPlot.Plotly` istogrammi) e layout (ad esempio singoli, sovrapposti e così via) usando la libreria.
* Possibilità di includere i pacchetti NuGet nel tuo notebook di C.

## <a name="next-steps"></a>Passaggi successivi

* [.NET per la documentazione di Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interattivo](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)