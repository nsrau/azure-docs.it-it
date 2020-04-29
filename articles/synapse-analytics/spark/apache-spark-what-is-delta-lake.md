---
title: Che cos'è Delta Lake
description: Panoramica di Delta Lake e del suo funzionamento come parte di Azure sinapsi Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429200"
---
# <a name="what-is-delta-lake"></a>Che cos'è Delta Lake?

Azure sinapsi Analytics è compatibile con Linux Foundation Delta Lake. Delta Lake è un livello di archiviazione open source che offre transazioni ACID (atomicità, coerenza, isolamento e durabilità) per Apache Spark e Big Data i carichi di lavoro.

## <a name="key-features"></a>Funzionalità principali

| Funzionalità | Descrizione |
| --- | --- |
| **Transazioni ACID** | I data Lake vengono in genere popolati tramite più processi e pipeline, alcuni dei quali scrivono simultaneamente dati con letture. Prima di Delta Lake e l'aggiunta di transazioni, gli ingegneri dei dati dovevano esaminare un processo manuale di errore per garantire l'integrità dei dati. Delta Lake porta le transazioni ACID note ai data Lake. Fornisce la serializzabilità, il livello di isolamento più forte. Per altre informazioni, vedere [immersione in Delta Lake: decomprimere il log delle transazioni](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Gestione dei metadati scalabile** | In Big Data, anche i metadati possono essere "Big Data". Delta Lake considera i metadati esattamente come i dati, sfruttando la potenza di elaborazione distribuita di Spark per gestire tutti i relativi metadati. Di conseguenza, Delta Lake è in grado di gestire in modo semplice le tabelle con scalabilità petabyte con miliardi di partizioni e file. |
| **Tempo di viaggio (controllo delle versioni dei dati)** | La possibilità di annullare una modifica o tornare a una versione precedente è una delle funzionalità principali delle transazioni. Delta Lake fornisce snapshot dei dati che consentono di ripristinare le versioni precedenti dei dati per controlli, rollback o per riprodurre esperimenti. Per altre informazioni, vedere [Introduzione al viaggio Delta Lake per i data Lake su larga scala](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Apri formato** | Apache parquet è il formato di base per Delta Lake, che consente di sfruttare gli efficienti schemi di compressione e codifica nativi del formato. |
| **Origine e sink di streaming e batch unificati** | Una tabella in Delta Lake è una tabella batch, nonché un'origine e un sink di streaming. L'inserimento di dati in streaming, il recupero cronologico batch e le query interattive funzionano tutti in modo predefinito. |
| **Imposizione dello schema** | L'imposizione dello schema consente di garantire che i tipi di dati siano corretti e che le colonne obbligatorie siano presenti, impedendo che i dati non validi provochino un'incoerenza dei dati. Per altre informazioni, vedere la pagina relativa [all'immersione in Delta Lake: applicazione dello Schema & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evoluzione dello schema** | Delta Lake consente di apportare modifiche a uno schema di tabella che può essere applicato automaticamente, senza la necessità di scrivere DDL di migrazione. Per altre informazioni, vedere la pagina relativa [all'immersione in Delta Lake: applicazione dello Schema & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Cronologia controlli** | Il log delle transazioni Delta Lake registra informazioni dettagliate su ogni modifica apportata ai dati che forniscono un audit trail completo delle modifiche. |
| **Aggiornamenti ed eliminazioni** | Delta Lake supporta le API scala/Java/Python e SQL per un'ampia gamma di funzionalità. Il supporto per le operazioni di merge, aggiornamento ed eliminazione consente di soddisfare i requisiti di conformità. Per altre informazioni, vedere [annuncio della versione Delta Lake 0.4.0](https://delta.io/news/delta-lake-0-4-0-released/) e [Upsert ed eliminazioni semplici e affidabili per le tabelle Delta Lake usando le API Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), che include frammenti di codice per i comandi di merge, aggiornamento ed eliminazione DML. |
| **100% compatibile con l'API Apache Spark** | Gli sviluppatori possono usare Delta Lake con le pipeline di dati esistenti con modifiche minime perché sono completamente compatibili con le implementazioni Spark esistenti. |

Per la documentazione completa, vedere la [pagina della documentazione di Delta Lake](https://docs.delta.io/latest/delta-intro.html)

Per altre informazioni, vedere [progetto Delta Lake](https://lfprojects.org).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
