---
title: Database condiviso
description: Azure Synapse Analytics offre un modello di metadati condivisi per cui la creazione di un database nel pool di Apache Spark serverless lo renderà accessibile dai motori del pool SQL serverless (anteprima) e del pool SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: e17eb44a5f4f4aace9ce9d541b8218b35db0f5d3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317830"
---
# <a name="azure-synapse-analytics-shared-database"></a>Database condiviso di Azure Synapse Analytics

Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Apache Spark serverless (anteprima) e il motore del pool SQL serverless (anteprima).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Un database creato con un processo Spark diventerà visibile con lo stesso nome a tutti i pool di Spark (anteprima) correnti e futuri nell'area di lavoro, incluso il motore del pool SQL serverless.

Il database predefinito di Spark, denominato `default`, sarà visibile anche nel contesto del pool SQL serverless come database denominato `default`.

Poiché i database vengono sincronizzati con il pool SQL serverless in modo asincrono, vengono visualizzati con un ritardo.

## <a name="manage-a-spark-created-database"></a>Gestire un database creato in Spark

Usare Spark per gestire i database creati in Spark. Ad esempio, eliminare un database tramite un processo del pool di Spark e crearvi tabelle da Spark.

Se si creano oggetti in un database creato in Spark usando il pool SQL serverless, o se si prova a eliminare il database, l'operazione riesce, ma il database Spark originale non viene modificato.

## <a name="how-name-conflicts-are-handled"></a>Come vengono gestiti i conflitti di nome

Se il nome di un database di Spark è in conflitto con il nome di un database del pool SQL serverless, nel pool SQL serverless viene aggiunto un suffisso al database di Spark. Il suffisso nel pool SQL serverless è `_<workspace name>-ondemand-DefaultSparkConnector`.

Ad esempio, se viene creato un database di Spark denominato `mydb` nell'area di lavoro di Azure Synapse `myws` ed esiste già un database del pool SQL serverless con lo stesso nome, è necessario usare il nome `mydb_myws-ondemand-DefaultSparkConnector` per fare riferimento al database di Spark nel pool SQL serverless.

> [!CAUTION]
> Attenzione: è opportuno non fare affidamento su questo comportamento.

## <a name="security-model"></a>Modello di protezione

Le tabelle e i database Spark, insieme alle relative rappresentazioni sincronizzate nel motore SQL, vengono protetti al livello di archiviazione sottostante.

L'entità di sicurezza che crea un database è considerata il proprietario del database e dispone di tutti i diritti sul database e sui relativi oggetti.

Per consentire a un'entità di sicurezza, ad esempio un utente o un gruppo di sicurezza, l'accesso a un database, fornire le autorizzazioni appropriate per file e cartelle POSIX alle cartelle e ai file sottostanti nella directory `warehouse`. 

Ad esempio, affinché un'entità di sicurezza possa leggere una tabella in un database, tutte le cartelle a partire dalla cartella del database nella directory `warehouse` devono avere le autorizzazioni `X` e `R` assegnate a tale entità di sicurezza. Inoltre i file, come i file di dati sottostanti della tabella, devono avere le autorizzazioni `R`. 

Se un'entità di sicurezza deve poter creare o eliminare oggetti in un database, sono necessarie ulteriori autorizzazioni `W` sulle cartelle e i file nella cartella `warehouse`.

## <a name="examples"></a>Esempi

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Creare e connettersi a un database di Spark con il pool SQL serverless

Creare prima di tutto un nuovo database Spark denominato `mytestdb` usando un cluster Spark che è già stato creato nell'area di lavoro. A questo scopo è possibile usare, ad esempio, un notebook C# Spark con l'istruzione .NET per Spark seguente:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Dopo una breve attesa, il database viene visualizzato nel pool SQL serverless. Ad esempio, eseguire l'istruzione seguente dal pool SQL serverless.

```sql
SELECT * FROM sys.databases;
```

Verificare che `mytestdb` sia incluso nei risultati.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui metadati condivisi di Azure Synapse Analytics](overview.md)
- [Altre informazioni sulle tabelle di metadati condivisi di Azure Synapse Analytics](table.md)
