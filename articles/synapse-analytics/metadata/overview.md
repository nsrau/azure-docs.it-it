---
title: Modello di metadati condivisi di Azure Synapse Analytics
description: Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Spark (anteprima), il motore SQL su richiesta (anteprima) e i pool SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420175"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Metadati condivisi di Azure Synapse Analytics

Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Spark (anteprima), il motore SQL su richiesta (anteprima) e i pool SQL.

[!INCLUDE [preview](../includes/note-preview.md)]



La condivisione supporta il cosiddetto modello di data warehouse moderno e fornisce ai motori SQL dell'area di lavoro l'accesso ai database e alle tabelle creati con Spark. Consente anche ai motori SQL di creare i propri oggetti, che non vengono condivisi con gli altri motori.

## <a name="support-the-modern-data-warehouse"></a>Supporto del data warehouse moderno

Il modello di metadati condivisi supporta il modello di data warehouse moderno nel modo seguente:

1. I dati del data lake vengono preparati e strutturati in modo efficiente con Spark archiviando i dati preparati in tabelle basate su Parquet, probabilmente partizionate e contenute probabilmente in più database.

2. I database creati da Spark e tutte le relative tabelle diventano visibili in una qualsiasi istanza del pool di Spark dell'area di lavoro di Azure Synapse e possono essere usati da qualsiasi processo Spark. Questa funzionalità è soggetta alle [autorizzazioni](#security-model-at-a-glance), poiché tutti i pool di Spark in un'area di lavoro condividono lo stesso archivio di metadati del catalogo sottostante.

3. I database creati da Spark e le relative tabelle basate su Paquet diventano visibili nel motore SQL su richiesta dell'area di lavoro. I [database](database.md) vengono creati automaticamente nei metadati di SQL su richiesta e le [tabelle esterne e gestite](table.md) create da un processo Spark vengono entrambe rese accessibili come tabelle esterne nei metadati di SQL su richiesta nello schema `dbo` del database corrispondente. <!--For more details, see [ADD LINK].-->

4. Se l'area di lavoro contiene istanze del pool SQL per le quali è abilitata la sincronizzazione dei metadati <!--[ADD LINK]--> o se viene creata una nuova istanza del pool SQL con la sincronizzazione dei metadati abilitata, viene automaticamente eseguito il mapping dei database creati da Spark e delle relative tabelle basate su Parquet nel database del pool SQL, come descritto in [Database condiviso di Azure Synapse Analytics](database.md).

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

La sincronizzazione degli oggetti avviene in modo asincrono. Gli oggetti vengono visualizzati nel contesto SQL con un leggero ritardo di alcuni secondi. Appena vengono visualizzati, possono essere sottoposti a query, ma non aggiornati o modificati dai motori SQL che sono autorizzati ad accedervi.

## <a name="which-metadata-objects-are-shared"></a>Quali oggetti di metadati vengono condivisi

Spark consente di creare database, tabelle esterne, tabelle gestite e viste. Poiché le viste di Spark richiedono un motore Spark che elabori l'istruzione Spark SQL e non possono essere elaborate da un motore SQL, solo i database e le relative tabelle esterne e gestite che usano il formato di archiviazione Parquet vengono condivise con i motori SQL dell'area di lavoro. Le viste di Spark vengono condivise solo tra le istanze del pool di Spark.

## <a name="security-model-at-a-glance"></a>Panoramica del modello di protezione

I database e le tabelle Spark, insieme alle relative rappresentazioni sincronizzate nei motori SQL, vengono protetti al livello di archiviazione sottostante. Quando la tabella viene sottoposta a query da uno dei motori che l'utente che ha inviato la query è autorizzato a usare, l'entità di sicurezza di tale utente viene passata ai file sottostanti. Le autorizzazioni vengono verificate al livello del file system.

Per altre informazioni, vedere [Database condiviso di Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Modificare la manutenzione

Se un oggetto di metadati viene eliminato o modificato con Spark, le modifiche vengono propagate al motore SQL su richiesta e ai pool SQL con gli oggetti sincronizzati. La sincronizzazione è asincrona e le modifiche vengono applicate ai motori SQL con un breve ritardo.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui database di metadati condivisi di Azure Synapse Analytics](database.md)
- [Altre informazioni sulle tabelle di metadati condivisi di Azure Synapse Analytics](table.md)

