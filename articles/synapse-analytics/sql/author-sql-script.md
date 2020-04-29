---
title: Script SQL in Azure sinapsi Studio (anteprima)
description: Introduzione agli script SQL di Azure sinapsi Studio (anteprima)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431072"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Uso di script SQL in Azure sinapsi Studio (anteprima)

Azure sinapsi Studio (anteprima) offre un'interfaccia Web per script SQL per la creazione di query SQL. È possibile connettersi al pool SQL (anteprima) o a SQL su richiesta (anteprima). 

## <a name="begin-authoring-in-sql-script"></a>Inizia la creazione nello script SQL 

Sono disponibili diversi modi per avviare l'esperienza di creazione nello script SQL. È possibile creare un nuovo script SQL mediante uno dei metodi seguenti.

1. Selezionare l'icona "+" e scegliere script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Dal menu azioni in sviluppare script SQL scegliere "nuovo script SQL" dal menu "azioni" in sviluppare script SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

o 

3. Scegliere "Importa" dal menu "azioni" in sviluppare script SQL e selezionare uno script SQL esistente dalla risorsa di archiviazione locale.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Creare lo script SQL

1. Scegliere un nome per lo script SQL selezionando il pulsante "proprietà" e sostituendo il nome predefinito assegnato allo script SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Scegliere il pool SQL specifico o SQL su richiesta dal menu a discesa "Connetti a". O, se necessario, scegliere il database da "use database".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Avviare la creazione dello script SQL utilizzando la funzionalità IntelliSense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Eseguire lo script SQL

Selezionare il pulsante "Esegui" per eseguire lo script SQL. I risultati vengono visualizzati per impostazione predefinita in una tabella.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Esportare i risultati

È possibile esportare i risultati nella risorsa di archiviazione locale in formati diversi, ad esempio CSV, Excel, JSON, XML, selezionando "Esporta risultati" e scegliendo l'estensione.

È inoltre possibile visualizzare i risultati dello script SQL in un grafico selezionando il pulsante "grafico". Selezionare "tipo di grafico" e "colonna Categoria". È possibile esportare il grafico in un'immagine selezionando "Salva come immagine". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Esplorare i dati da un file parquet.

È possibile esplorare i file parquet in un account di archiviazione usando uno script SQL per visualizzare in anteprima il contenuto del file. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelle SQL, tabelle esterne, viste

Selezionando il menu "azioni" sotto dati, è possibile selezionare diverse azioni, ad esempio: "nuovo script SQL", "seleziona le prime 1000 righe", "Crea", "Elimina e crea". Per esplorare il gesto disponibile, fare clic con il pulsante destro del mouse sui nodi del pool SQL e su SQL su richiesta.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di uno script SQL, vedere [analisi delle sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics).