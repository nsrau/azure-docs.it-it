---
title: Database condiviso di Azure Synapse Analytics
description: Azure Synapse Analytics offre un modello di metadati condivisi che consente di creare una tabella in Apache Spark accessibile dai motori SQL su richiesta (anteprima) e di pool SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: da1bd9c812c20f60264d1a5ee1f8821128900618
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698862"
---
# <a name="azure-synapse-analytics-shared-database"></a>Database condiviso di Azure Synapse Analytics

Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Spark (anteprima) e il motore SQL su richiesta (anteprima).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Un database creato con un processo Spark diventerà visibile con lo stesso nome a tutti i pool di Spark (anteprima) correnti e futuri nell'area di lavoro e nel motore SQL su richiesta.

Il database predefinito Spark, denominato `default`, sarà visibile anche nel contesto SQL su richiesta come database denominato `default`.

Poiché i database vengono sincronizzati con SQL su richiesta in modo asincrono, vengono visualizzati con un ritardo.

## <a name="manage-a-spark-created-database"></a>Gestire un database creato in Spark

Usare Spark per gestire i database creati in Spark. Ad esempio, eliminare un database tramite un processo del pool di Spark e crearvi tabelle da Spark.

Se si creano oggetti in un database creato in Spark usando SQL su richiesta o se si cerca di eliminare il database, l'operazione riesce, ma il database Spark originale non viene modificato.

## <a name="handling-of-name-conflicts"></a>Gestione dei conflitti tra nomi

Se il nome di un database Spark è in conflitto con il nome di un database SQL su richiesta esistente, in SQL su richiesta viene aggiunto un suffisso al database Spark. Il suffisso in SQL su richiesta è `_<workspace name>-ondemand-DefaultSparkConnector`.

Ad esempio, se viene creato un database Spark denominato `mydb` nell'area di lavoro di Azure Synapse `myws` ed esiste già un database SQL su richiesta con lo stesso nome, è necessario fare riferimento al database Spark in SQL su richiesta usando il nome `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Attenzione: è opportuno non fare affidamento su questo comportamento.

## <a name="security-model"></a>Modello di protezione

Le tabelle e i database Spark, insieme alle relative rappresentazioni sincronizzate nel motore SQL, vengono protetti al livello di archiviazione sottostante.

L'entità di sicurezza che crea un database è considerata il proprietario del database e dispone di tutti i diritti sul database e sui relativi oggetti.

Per consentire a un'entità di sicurezza, ad esempio un utente o un gruppo di sicurezza, l'accesso a un database, fornire le autorizzazioni appropriate per file e cartelle POSIX alle cartelle e ai file sottostanti nella directory `warehouse`. 

Ad esempio, affinché un'entità di sicurezza possa leggere una tabella in un database, tutte le cartelle a partire dalla cartella del database nella directory `warehouse` devono avere le autorizzazioni `X` e `R` assegnate a tale entità di sicurezza. Inoltre i file, come i file di dati sottostanti della tabella, devono avere le autorizzazioni `R`. 

Se un'entità di sicurezza deve poter creare o eliminare oggetti in un database, sono necessarie ulteriori autorizzazioni `W` sulle cartelle e i file nella cartella `warehouse`.

## <a name="examples"></a>Esempi

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Creare e connettersi a un database Spark - SQL su richiesta

Creare prima di tutto un nuovo database Spark denominato `mytestdb` usando un cluster Spark che è già stato creato nell'area di lavoro. A questo scopo è possibile usare, ad esempio, un notebook C# Spark con l'istruzione .NET per Spark seguente:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Dopo una breve attesa, il database viene visualizzato in SQL su richiesta. Ad esempio, eseguire l'istruzione seguente da SQL su richiesta.

```sql
SELECT * FROM sys.databases;
```

Verificare che `mytestdb` sia incluso nei risultati.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui metadati condivisi di Azure Synapse Analytics](overview.md)
- [Altre informazioni sulle tabelle di metadati condivisi di Azure Synapse Analytics](table.md)
