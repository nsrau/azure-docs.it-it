---
title: Script SQL in Azure sinapsi Studio (anteprima)
description: Introduzione agli script SQL di Azure sinapsi Studio (anteprima)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 3618ba28403882ee1f949a2bbc97e586674b3772
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317525"
---
# <a name="using-sql-scripts-in-azure-synapse-studio-preview"></a>Uso di script SQL in Azure sinapsi Studio (anteprima)

Azure sinapsi Studio (anteprima) offre un'interfaccia Web per script SQL per la creazione di query SQL. È possibile connettersi al pool SQL (anteprima). 

## <a name="begin-authoring-in-sql-script"></a>Inizia la creazione nello script SQL 

Sono disponibili diversi modi per avviare l'esperienza di creazione nello script SQL. È possibile creare un nuovo script SQL mediante uno dei metodi seguenti.

1. Dal menu sviluppo selezionare l'icona **"+"** e scegliere **script SQL**.

2. Scegliere **nuovo script SQL** dal menu **azioni** .

3. Scegliere **Importa** dal menu **Azioni** in sviluppare script SQL. Selezionare uno script SQL esistente dalla risorsa di archiviazione locale.
![nuove azioni script SQL 3](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Creare lo script SQL

1. Scegliere un nome per lo script SQL selezionando il pulsante della **Proprietà** e sostituendo il nome predefinito assegnato allo script SQL. 
![nuova Rinomina script SQL](media/author-sql-script/new-sql-script-rename.png)

2. Scegliere il pool SQL dedicato specifico o il pool SQL senza server dal menu **a discesa Connetti a** . Se necessario, scegliere il database da **Usa database**. 
![nuovo pool SQL Choose](media/author-sql-script/new-sql-choose-pool.png)

3. Avviare la creazione dello script SQL utilizzando la funzionalità IntelliSense.

## <a name="run-your-sql-script"></a>Eseguire lo script SQL

Selezionare il pulsante **Esegui** per eseguire lo script SQL. I risultati vengono visualizzati per impostazione predefinita in una tabella.

![nuova tabella dei risultati dello script SQL](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Esportare i risultati

È possibile esportare i risultati nella risorsa di archiviazione locale in formati diversi, ad esempio CSV, Excel, JSON, XML, selezionando "Esporta risultati" e scegliendo l'estensione.

È inoltre possibile visualizzare i risultati dello script SQL in un grafico selezionando il pulsante **grafico** . Selezionare il tipo di grafico e la **colonna Categoria**. È possibile esportare il grafico in un'immagine selezionando **Salva come immagine**. 

![nuovo grafico dei risultati dello script SQL](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Esplorare i dati da un file parquet

È possibile esplorare i file parquet in un account di archiviazione usando uno script SQL per visualizzare in anteprima il contenuto del file.

![nuovo script sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelle SQL, tabelle esterne, viste

Selezionando il menu **azioni** in dati, è possibile selezionare diverse azioni, ad esempio:

- Nuovo script SQL
- Selezionare le prime 1000 righe
- CREATE
- Elimina e crea 
 
Per esplorare il gesto disponibile, fare clic con il pulsante destro del mouse sui nodi dei database SQL.
 
![nuovo database script](media/author-sql-script/new-script-database.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di uno script SQL, vedere [analisi delle sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics).
