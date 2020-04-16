---
title: Cos'è il lago Delta
description: Panoramica di Delta Lake e del suo funzionamento nell'ambito di Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429200"
---
# <a name="what-is-delta-lake"></a>Che cos'è Delta Lake?

Azure Synapse Analytics è compatibile con Linux Foundation Delta Lake. Delta Lake è un livello di storage open source che porta le transazioni ACID (atomicità, coerenza, isolamento e durata) nei carichi di lavoro Apache Spark e Big Data.

## <a name="key-features"></a>Funzionalità principali

| Funzionalità | Descrizione |
| --- | --- |
| **Transazioni ACID** | I data lake vengono in genere popolati tramite più processi e pipeline, alcuni dei quali scrivono dati contemporaneamente alle letture. Prima di Delta Lake e dell'aggiunta di transazioni, gli ingegneri dei dati dovevano passare attraverso un processo manuale soggetto a errori per garantire l'integrità dei dati. Delta Lake porta le transazioni ACID familiari nei data lake. Fornisce serializzabilità, il livello più forte di livello di isolamento. Per ulteriori informazioni, vedere [Diving into Delta Lake: unificando il registro delle transazioni](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Gestione dei metadati scalabiliScalabilible Metadata Handling** | Nei Big Data, anche i metadati stessi possono essere "big data". Delta Lake tratta i metadati come i dati, sfruttando la potenza di elaborazione distribuita di Spark per gestire tutti i metadati. Di conseguenza, Delta Lake è in grado di gestire tabelle su scala petabyte con miliardi di partizioni e file a proprio agio. |
| **Viaggio nel tempo (controllo delle versioni dei dati)** | La possibilità di "annullare" una modifica o tornare a una versione precedente è una delle caratteristiche chiave delle transazioni. Delta Lake fornisce istantanee dei dati che consentono di ripristinare versioni precedenti dei dati per i controlli, i rollback o la riproduzione di esperimenti. Per ulteriori informazioni, vedere [Introducing Delta Lake Time Travel for Large Scale Data Lakes](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Formato aperto** | Apache Parquet è il formato di base per Delta Lake, che consente di sfruttare gli efficienti schemi di compressione e codifica nativi del formato. |
| **Origine e sink di batch e streaming unificati** | Una tabella in Delta Lake è sia una tabella batch, sia un'origine di flusso e un sink. L'inserimento di dati in streaming, il riempimento cronologico in batch e le query interattive funzionano tutti fuori dagli schemi. |
| **Applicazione dello schemaSchema Enforcement** | L'applicazione dello schema garantisce che i tipi di dati siano corretti e che le colonne obbligatorie siano presenti, evitando che i dati non corretti causino un'incoerenza dei dati. Per ulteriori informazioni, vedere [Immersioni nel lago Delta: applicazione dello schema & evoluzione](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evoluzione dello schema** | Delta Lake consente di apportare modifiche a uno schema di tabella che può essere applicato automaticamente, senza dover scrivere DDL di migrazione. Per ulteriori informazioni, vedere [Immersioni nel lago Delta: applicazione dello schema & evoluzione](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Cronologia di controllo** | Il log delle transazioni di Delta Lake registra i dettagli relativi a tutte le modifiche apportate ai dati fornendo un audit trail completo delle modifiche. |
| **Aggiornamenti ed eliminazioni** | Delta Lake supporta le API Scala / Java / Python e SQL per una varietà di funzionalità. Il supporto per le operazioni di unione, aggiornamento ed eliminazione consente di soddisfare i requisiti di conformità. Per ulteriori informazioni, vedere [Annuncio del Delta Lake 0.4.0 Release](https://delta.io/news/delta-lake-0-4-0-released/) e [Simple, Reliable Upserts and Deletes on Delta Lake Tables utilizzando le API Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), che include frammenti di codice per i comandi DML di unione, aggiornamento ed eliminazione. |
| **Compatibile al 100% con l'API Apache Spark** | Gli sviluppatori possono utilizzare Delta Lake con le pipeline di dati esistenti con modifiche minime in quanto è completamente compatibile con le implementazioni Spark esistenti. |

Per la documentazione completa, vedere la [pagina di documentazione](https://docs.delta.io/latest/delta-intro.html) di Delta Lake

Per ulteriori informazioni, vedere [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Passaggi successivi

- [.NET per la documentazione di Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
