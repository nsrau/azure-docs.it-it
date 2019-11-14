---
title: Usare la connessione Open Database Connectivity (ODBC) ad Azure Esplora dati per visualizzare i dati con tableau
description: Questo articolo illustra come usare una connessione Open Database Connectivity (ODBC) ad Azure Esplora dati connessione per visualizzare i dati con tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4dd8fbd761a3442536919e17bae5465adf6b945f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023854"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizzare i dati da Esplora dati di Azure in tableau

 [Tableau](https://www.tableau.com/) è una piattaforma di analisi visiva per Business Intelligence. Per connettersi ad Azure Esplora dati da tableau e importare dati da un cluster di esempio, usare il driver SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>prerequisiti

Per completare questo articolo, è necessario quanto segue:

* [Connettersi ad azure Esplora dati con ODBC](connect-odbc.md) usando il driver ODBC SQL Server per connettersi ad Azure Esplora dati da tableau. 

* Tableau desktop, completo o versione di [valutazione](https://www.tableau.com/products/desktop/download) .

* Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere [creare un cluster e un database di azure Esplora dati](create-cluster-database-portal.md) e [inserire dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizzare i dati in tableau 

Al termine della configurazione di ODBC, è possibile importare dati di esempio in tableau.

1. In Tableau desktop, nel menu a sinistra, selezionare **altri database (ODBC)** .

    ![Connettersi con ODBC](media/tableau/connect-odbc.png)

1. Per **DSN**selezionare l'origine dati creata per ODBC, quindi selezionare **Accedi**.

    ![Accesso ODBC](media/tableau/odbc-sign-in.png)

1. Per **database**, selezionare il database nel cluster di esempio, ad esempio *TestDatabase*. Per **schema**selezionare *dbo*e per **tabella**selezionare la tabella di esempio *StormEvents* .

    ![Selezione database e tabella](media/tableau/select-database-table.png)

1. Tableau Mostra ora lo schema per i dati di esempio. Selezionare **Aggiorna adesso** per visualizzare i dati in tableau.

    ![Aggiornare i dati](media/tableau/update-data.png)

    Quando i dati vengono importati, tableau Mostra righe di dati simili all'immagine seguente.

    ![Set di risultati](media/tableau/result-set.png)

1. Ora è possibile creare visualizzazioni in tableau in base ai dati portati da Azure Esplora dati. Per altre informazioni, vedere [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)