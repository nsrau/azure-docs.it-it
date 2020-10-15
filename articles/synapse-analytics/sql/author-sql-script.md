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
ms.openlocfilehash: cd00c5033d0435b24d4cfb9f413b5afe74da6774
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089010"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Uso di script SQL in Azure sinapsi Studio (anteprima)

Azure sinapsi Studio (anteprima) offre un'interfaccia Web per script SQL per la creazione di query SQL. È possibile connettersi al pool SQL (anteprima) o a SQL su richiesta (anteprima). 

## <a name="begin-authoring-in-sql-script"></a>Inizia la creazione nello script SQL 

Sono disponibili diversi modi per avviare l'esperienza di creazione nello script SQL. È possibile creare un nuovo script SQL mediante uno dei metodi seguenti.

1. Dal menu sviluppo selezionare l'icona **"+"** e scegliere **script SQL**.

![nuovo script SQL](media/author-sql-script/newsqlscript.png)

2. Scegliere **nuovo script SQL**dal menu **azioni** .
> [!div class="mx-imgBorder"]
> ![nuove azioni script SQL 2](media/author-sql-script/newsqlscript2actions.png)

In alternativa è possibile: 

3. Scegliere **Importa** dal menu **Azioni** in sviluppare script SQL. Selezionare uno script SQL esistente dalla risorsa di archiviazione locale.
![nuove azioni script SQL 3](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Creare lo script SQL

1. Scegliere un nome per lo script SQL selezionando il pulsante della **Proprietà** e sostituendo il nome predefinito assegnato allo script SQL. 
![nuova Rinomina script SQL](media/author-sql-script/newsqlscriptrename.png)

2. Scegliere il pool SQL specifico o SQL su richiesta dal menu a discesa **Connetti a** . Se necessario, scegliere il database da **Usa database**. 
![nuovo pool SQL Choose](media/author-sql-script/newsqlchoosepool.png)

3. Avviare la creazione dello script SQL utilizzando la funzionalità IntelliSense.
![nuova IntelliSense SQL](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Eseguire lo script SQL

Selezionare il pulsante **Esegui** per eseguire lo script SQL. I risultati vengono visualizzati per impostazione predefinita in una tabella.

![nuova tabella dei risultati dello script SQL](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Esportare i risultati

È possibile esportare i risultati nella risorsa di archiviazione locale in formati diversi, ad esempio CSV, Excel, JSON, XML, selezionando "Esporta risultati" e scegliendo l'estensione.

È inoltre possibile visualizzare i risultati dello script SQL in un grafico selezionando il pulsante **grafico** . Selezionare il tipo di grafico e la **colonna Categoria**. È possibile esportare il grafico in un'immagine selezionando **Salva come immagine**. 

![nuovo grafico dei risultati dello script SQL](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Esplorare i dati da un file parquet

È possibile esplorare i file parquet in un account di archiviazione usando uno script SQL per visualizzare in anteprima il contenuto del file.

![nuovo script sqlod parquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelle SQL, tabelle esterne, viste

Selezionando il menu **azioni** in dati, è possibile selezionare diverse azioni, ad esempio:

- Nuovo script SQL
- Selezionare le prime 1000 righe
- CREATE
- Elimina e crea 
 
Per esplorare il gesto disponibile, fare clic con il pulsante destro del mouse sui nodi del pool SQL e su SQL su richiesta.
 
![nuovo database script](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di uno script SQL, vedere [analisi delle sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics).
