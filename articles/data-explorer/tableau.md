---
title: Usare il connettore ODBC di Azure Data Explorer per visualizzare i dati TableauUse Azure Data Explorer ODBC connector to visualize Tableau data
description: In this article, you learn how to use an Open Database Connectivity (ODBC) connection to Azure Data Explorer connection to visualize data with Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562446"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizzare i dati da Azure Data Explorer in TableauDisplay data from Azure Data Explorer in Tableau

 [Tableau](https://www.tableau.com/) è una piattaforma di analisi visiva per la business intelligence. Per connettersi a Esplora dati di Azure da Tableau e importare dati da un cluster di esempio, usare il driver ODBC (Open Database Connectivity) di SQL Server.To connect to Azure Data Explorer from Tableau and bring in data from a sample cluster, use the SQL Server Open Database Connectivity (ODBC) driver. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessario quanto segue:

* [Connettersi ad Esplora dati](connect-odbc.md) di Azure con ODBC usando il driver ODBC di SQL Server per connettersi ad Azure Data Explorer da Tableau.Connect to Azure Data Explorer with ODBC using the SQL Server ODBC driver, to connect to Azure Data Explorer from Tableau. 

* Tableau Desktop, versione completa o [di prova.](https://www.tableau.com/products/desktop/download)

* Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere Creare un cluster e un database di Azure Data Explorer e eseguire l'inserimento di dati di esempio in Azure Data Explorer.For more information, see [Create an Azure Data Explorer cluster](create-cluster-database-portal.md) and database and [Ingest sample data into Azure Data Explorer.](ingest-sample-data.md)

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizzare i dati in Tableau 

Dopo aver completato la configurazione di ODBC, è possibile inserire dati di esempio in Tableau.Once you've finished configuring ODBC, you can bring sample data into Tableau.

1. In Tableau Desktop, nel menu a sinistra, selezionare **Altri database (ODBC)**.

    ![Connettersi con ODBC](media/tableau/connect-odbc.png)

1. Per **DSN**, selezionare l'origine dati creata per ODBC, quindi selezionare **Accedi**.

    ![Accesso ODBC](media/tableau/odbc-sign-in.png)

1. In **Database**selezionare il database nel cluster di esempio, ad esempio *TestDatabase*. Per **Schema**, selezionare *dbo*e per **Tabella**selezionare la tabella di esempio *StormEvents* .

    ![Selezionare il database e la tabella](media/tableau/select-database-table.png)

1. Tableau ora mostra lo schema per i dati di esempio. Selezionare **Aggiorna ora** per portare i dati in Tableau.

    ![Aggiornare i dati](media/tableau/update-data.png)

    Quando i dati vengono importati, Tableau mostra righe di dati simili all'immagine seguente.

    ![Set di risultati](media/tableau/result-set.png)

1. A questo punto è possibile creare visualizzazioni in Tableau in base ai dati creati da Azure Data Explorer.Now you can create visualizations in Tableau based on the data you brought in from Azure Data Explorer. Per ulteriori informazioni, vedere [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)