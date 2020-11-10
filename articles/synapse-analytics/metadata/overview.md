---
title: Modello di metadati condivisi
description: Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Apache Spark serverless (anteprima), il pool SQL serverless (anteprima) e i pool SQL dedicati.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 64c19f3331be8ffda433207da88ebf22c546ee4e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324666"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Metadati condivisi di Azure Synapse Analytics

Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Apache Spark serverless (anteprima) e il pool SQL serverless (anteprima).

[!INCLUDE [preview](../includes/note-preview.md)]

La condivisione supporta il cosiddetto modello di data warehouse moderno e fornisce ai motori SQL dell'area di lavoro l'accesso ai database e alle tabelle creati con Spark. Consente anche ai motori SQL di creare i propri oggetti, che non vengono condivisi con gli altri motori.

## <a name="support-the-modern-data-warehouse"></a>Supporto del data warehouse moderno

Il modello di metadati condivisi supporta il modello di data warehouse moderno nel modo seguente:

1. I dati del data lake vengono preparati e strutturati in modo efficiente con Spark archiviando i dati preparati in tabelle basate su Parquet, probabilmente partizionate e contenute probabilmente in più database.

2. I database creati da Spark e tutte le relative tabelle diventano visibili in una qualsiasi istanza del pool di Spark dell'area di lavoro di Azure Synapse e possono essere usati da qualsiasi processo Spark. Questa funzionalità è soggetta alle [autorizzazioni](#security-model-at-a-glance), poiché tutti i pool di Spark in un'area di lavoro condividono lo stesso archivio di metadati del catalogo sottostante.

3. I database creati da Spark e le relative tabelle basate su Parquet diventano visibili nel pool SQL serverless dell'area di lavoro. I [database](database.md) vengono creati automaticamente nei metadati del pool SQL serverless e le [tabelle esterne e gestite](table.md) create da un processo Spark vengono entrambe rese accessibili come tabelle esterne nei metadati del pool SQL serverless nello schema `dbo` del database corrispondente. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

La sincronizzazione degli oggetti avviene in modo asincrono. Gli oggetti vengono visualizzati nel contesto SQL con un leggero ritardo di alcuni secondi. Appena vengono visualizzati, possono essere sottoposti a query, ma non aggiornati o modificati dai motori SQL che sono autorizzati ad accedervi.

## <a name="shared-metadata-objects"></a>Oggetti metadati condivisi

Spark consente di creare database, tabelle esterne, tabelle gestite e viste. Poiché le viste di Spark richiedono un motore Spark che elabori l'istruzione Spark SQL e non possono essere elaborate da un motore SQL, solo i database e le relative tabelle esterne e gestite che usano il formato di archiviazione Parquet vengono condivise con il motore SQL dell'area di lavoro. Le viste di Spark vengono condivise solo tra le istanze del pool di Spark.

## <a name="security-model-at-a-glance"></a>Panoramica del modello di protezione

I database e le tabelle Spark, insieme alle relative rappresentazioni sincronizzate nel motore SQL, vengono protetti al livello di archiviazione sottostante. Quando la tabella viene sottoposta a query da uno dei motori che l'utente che ha inviato la query è autorizzato a usare, l'entità di sicurezza di tale utente viene passata ai file sottostanti. Le autorizzazioni vengono verificate al livello del file system.

Per altre informazioni, vedere [Database condiviso di Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Modificare la manutenzione

Se un oggetto di metadati viene eliminato o modificato con Spark, le modifiche vengono propagate al pool SQL serverless. La sincronizzazione è asincrona e le modifiche vengono applicate al motore SQL con un breve ritardo.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui database di metadati condivisi di Azure Synapse Analytics](database.md)
- [Altre informazioni sulle tabelle di metadati condivisi di Azure Synapse Analytics](table.md)

