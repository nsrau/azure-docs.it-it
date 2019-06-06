---
title: Utilizzare connessione Open Database Connectivity (ODBC) a Esplora dati di Azure per visualizzare i dati con Tableau
description: In questo articolo descrive come usare una connessione di Open Database Connectivity (ODBC) per la connessione Esplora dati di Azure per visualizzare i dati con Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499085"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizzare i dati da Esplora dati di Azure in Tableau

 [Tableau](https://www.tableau.com/) è una piattaforma analitica visual per business intelligence. Per connettersi a Esplora dati di Azure da Tableau e importare dati da un cluster di esempio, usare il driver SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue per completare questo articolo:

* [Connettersi a Esplora dati di Azure con ODBC](connect-odbc.md) Usa il driver ODBC di SQL Server, per connettersi a Esplora dati di Azure da Tableau. 

* Tableau Desktop completo, o [valutazione](https://www.tableau.com/products/desktop/download) versione.

* Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere [creare un cluster di Esplora dati di Azure e un database](create-cluster-database-portal.md) e [inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizzare i dati in Tableau 

Dopo aver completato la configurazione di ODBC, è possibile importare i dati di esempio in Tableau.

1. In Tableau Desktop, nel menu a sinistra, selezionare **altri database (ODBC)** .

    ![Connettersi con ODBC](media/tableau/connect-odbc.png)

1. Per la **DSN**, selezionare l'origine dati creata per ODBC, quindi scegliere **Accedi**.

    ![Accedi ODBC](media/tableau/odbc-sign-in.png)

1. Per la **Database**, selezionare il database nel cluster di esempio, ad esempio *TestDatabase*. Per la **Schema**, selezionare *dbo*e per **tabella**, selezionare il *StormEvents* tabella di esempio.

    ![Selezionare database e tabella](media/tableau/select-database-table.png)

1. Tableau ora mostra lo schema per i dati di esempio. Selezionare **Aggiorna adesso** per inserire i dati in Tableau.

    ![Aggiornare i dati](media/tableau/update-data.png)

    Quando i dati vengono importati, Tableau Mostra le righe di dati simili all'immagine seguente.

    ![Set di risultati](media/tableau/result-set.png)

1. A questo punto è possibile creare visualizzazioni in Tableau basate sui dati inseriti da Esplora dati di Azure. Per altre informazioni, vedere [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)