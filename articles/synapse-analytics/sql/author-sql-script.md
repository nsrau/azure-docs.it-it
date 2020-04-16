---
title: Script SQL in Azure Synapse Studio (anteprima)SQL scripts in Azure Synapse Studio (preview)
description: Introduzione agli script SQL di Azure Synapse Studio (anteprima)Introduction Azure Synapse Studio (preview) SQL scripts
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431072"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Uso dello script SQL in Azure Synapse Studio (anteprima)Using SQL script in Azure Synapse Studio (preview)

Azure Synapse Studio (anteprima) offre un'interfaccia Web di script SQL per la creazione di query SQL. È possibile connettersi al pool SQL (anteprima) o SQL su richiesta (anteprima). 

## <a name="begin-authoring-in-sql-script"></a>Iniziare la creazione nello script SQLBegin authoring in SQL script 

Esistono diversi modi per avviare l'esperienza di creazione nello script SQL. È possibile creare un nuovo script SQL tramite uno dei metodi descritti di seguito.

1. Selezionare l'icona "- " e scegliere lo script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Dal menu Azioni in Sviluppo script SQL Scegliere "Nuovo script SQL" dal menu "Azioni" in Sviluppo script SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

o 

3. Scegliere "Importa" dal menu "Azioni" in Sviluppo script SQL e selezionare uno script SQL esistente dall'archivio locale.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Creare lo script SQLCreate your SQL script

1. Scegliere un nome per lo script SQL selezionando il pulsante "Proprietà" e sostituendo il nome predefinito assegnato allo script SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Scegliere il pool SQL specifico o il pool SQL su richiesta dal menu a discesa "Connetti a". In alternativa, se necessario, scegliere il database da "Usa database".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Iniziare a creare lo script SQL utilizzando la funzionalità di intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Eseguire lo script SQLRun your SQL script

Selezionare il pulsante "Esegui" per eseguire lo script SQL. I risultati vengono visualizzati per impostazione predefinita in una tabella.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Esportare i risultati

È possibile esportare i risultati nell'archivio locale in diversi formati (inclusi CSV, Excel, JSON, XML) selezionando "Esporta risultati" e scegliendo l'estensione.

È inoltre possibile visualizzare i risultati dello script SQL in un grafico selezionando il pulsante "Grafico". Selezionare il "Tipo di grafico" e "Colonna categoria". È possibile esportare il grafico in un'immagine selezionando "Salva come immagine". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Esplorare i dati da un file di parquet.

È possibile esplorare i file di parquet in un account di archiviazione usando uno script SQL per visualizzare in anteprima il contenuto del file. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelle SQL, tabelle esterne, viste

Selezionando il menu "Azioni" sotto i dati, è possibile selezionare diverse azioni come: "Nuovo script SQL", "Selezionare TOP 1000 righe", "CREATE", "DROP e CREATE". Esplorare il movimento disponibile facendo clic con il pulsante destro del mouse sui nodi del pool SQL e SQL su richiesta.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di uno script SQL, vedere [Analisi synapse](https://docs.microsoft.com/azure/synapse-analytics)di Azure.