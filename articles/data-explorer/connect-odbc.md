---
title: Connettersi a Esplora dati di Azure con ODBC
description: In questa procedura descrive come configurare una connessione ODBC a Esplora dati di Azure, quindi usare tale connessione per visualizzare i dati con Tableau.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 0ec1c2f4fc412ca6c81e179d0ad22f781b896357
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757615"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Connettersi a Esplora dati di Azure con ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) è una diffuso application programming interface (API) per l'accesso al database. Usare ODBC per connettersi a Esplora dati di Azure dalle applicazioni che non hanno un connettore dedicato.

Dietro le quinte, le applicazioni chiamano funzioni nell'interfaccia di ODBC, che vengono implementate nei moduli specifici del database denominati *driver*. Esplora i dati di Azure supporta un subset del protocollo di comunicazione di SQL Server ([MS-TDS](/azure/kusto/api/tds/)); pertanto è possibile usare il driver ODBC per SQL Server.

In questo articolo descrive come usare il driver ODBC di SQL Server, pertanto è possibile connettersi a Esplora dati di Azure da qualsiasi applicazione che supporti ODBC. È possibile quindi, facoltativamente, connettersi a Esplora dati di Azure da Tableau e importare dati da un cluster di esempio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura, sono necessari gli elementi seguenti:

* [Microsoft ODBC Driver for SQL Server versione 17.2.0.1 o in un secondo momento](/sql/connect/odbc/download-odbc-driver-for-sql-server) per il sistema operativo.

* Se si vuole seguire questo esempio Tableau, è anche necessario:

  * Tableau Desktop completo oppure [valutazione](https://www.tableau.com/products/desktop/download) versione.

  * Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere [Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure](create-cluster-database-portal.md) e [Inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Configurare l'origine dati ODBC

Seguire questi passaggi per configurare un'origine dati ODBC tramite il driver ODBC per SQL Server.

1. In Windows, cercare *origini dati ODBC*e aprire l'app desktop origini dati ODBC.

1. Selezionare **Aggiungi**.

    ![Aggiungere l'origine dati](media/connect-odbc/add-data-source.png)

1. Selezionare **ODBC Driver 17 for SQL Server** quindi **fine**.

    ![Selezione del driver](media/connect-odbc/select-driver.png)

1. Immettere un nome e una descrizione per la connessione e il cluster che si desidera connettersi, quindi selezionare **successivo**. Il cluster URL deve essere nel formato  *\<ClusterName\>.\< Area geografica\>. kusto.windows.net*.

    ![Selezionare il server](media/connect-odbc/select-server.png)

1. Selezionare **integrata di Active Directory** quindi **successivo**.

    ![Integrato in Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selezionare quindi il database con i dati di esempio **successivo**.

    ![Database di modifica predefinito](media/connect-odbc/change-default-database.png)

1. Nella schermata successiva, lasciare tutte le opzioni come valore predefinito è quindi selezionare **fine**.

1. Selezionare **origine dati di Test**.

    ![Verifica origine dati](media/connect-odbc/test-data-source.png)

1. Verificare che il test ha avuto esito positivo quindi selezionare **OK**. Se il test non è riuscito, controllare i valori specificati nei passaggi precedenti e assicurarsi di che disporre di autorizzazioni sufficienti per connettersi al cluster.

    ![Test ha avuto esito positivo](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Visualizzare i dati in Tableau (facoltativo)

Dopo aver completato la configurazione ODBC, è possibile importare i dati di esempio in Tableau.

1. In Tableau Desktop, nel menu a sinistra, selezionare **altri database (ODBC)**.

    ![Connettersi con ODBC](media/connect-odbc/connect-odbc.png)

1. Per la **DSN**, selezionare l'origine dati creata per ODBC, quindi scegliere **Accedi**.

    ![Accedi ODBC](media/connect-odbc/odbc-sign-in.png)

1. Per la **Database**, selezionare il database nel cluster di esempio, ad esempio *TestDatabase*. Per la **Schema**, selezionare *dbo*e per **tabella**, selezionare il *StormEvents* tabella di esempio.

    ![Selezionare database e tabella](media/connect-odbc/select-database-table.png)

1. Tableau ora mostra lo schema per i dati di esempio. Selezionare **Aggiorna adesso** per inserire i dati in Tableau.

    ![Aggiornare i dati](media/connect-odbc/update-data.png)

    Quando i dati vengono importati, Tableau Mostra le righe di dati simili all'immagine seguente.

    ![Set di risultati](media/connect-odbc/result-set.png)

1. A questo punto è possibile creare visualizzazioni in Tableau basate sui dati inseriti da Esplora dati di Azure. Per altre informazioni, vedere [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query per Esplora dati di Azure](write-queries.md)

[Esercitazione: Visualizzare i dati da Esplora dati di Azure in Power BI](visualize-power-bi.md)