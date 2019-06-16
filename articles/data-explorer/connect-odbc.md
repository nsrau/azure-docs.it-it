---
title: Connettersi a Esplora dati di Azure con ODBC
description: In questo articolo descrive come configurare una connessione di Open Database Connectivity (ODBC) a Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494838"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Connettersi a Esplora dati di Azure con ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) è una diffuso application programming interface (API) per l'accesso al database. Usare ODBC per connettersi a Esplora dati di Azure dalle applicazioni che non hanno un connettore dedicato.

Dietro le quinte, le applicazioni chiamano funzioni nell'interfaccia di ODBC, che vengono implementate nei moduli specifici del database denominati *driver*. Esplora i dati di Azure supporta un subset del protocollo di comunicazione di SQL Server ([MS-TDS](/azure/kusto/api/tds/)), pertanto è possibile usare il driver ODBC per SQL Server.

In questo articolo descrive come usare il driver ODBC di SQL Server, pertanto è possibile connettersi a Esplora dati di Azure da qualsiasi applicazione che supporti ODBC.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

* [Microsoft ODBC Driver for SQL Server versione 17.2.0.1 o in un secondo momento](/sql/connect/odbc/download-odbc-driver-for-sql-server) per il sistema operativo.

## <a name="configure-the-odbc-data-source"></a>Configurare l'origine dati ODBC

Seguire questi passaggi per configurare un'origine dati ODBC tramite il driver ODBC per SQL Server.

1. In Windows, cercare *origini dati ODBC*e aprire l'app desktop origini dati ODBC.

1. Selezionare **Aggiungi**.

    ![Aggiungere l'origine dati](media/connect-odbc/add-data-source.png)

1. Selezionare **ODBC Driver 17 for SQL Server** quindi **fine**.

    ![Selezione del driver](media/connect-odbc/select-driver.png)

1. Immettere un nome e una descrizione per la connessione e il cluster che si desidera connettersi, quindi selezionare **successivo**. Il cluster URL deve essere nel formato  *\<ClusterName\>.\< Area geografica\>. kusto.windows.net*.

    ![Selezionare un server](media/connect-odbc/select-server.png)

1. Selezionare **integrata di Active Directory** quindi **successivo**.

    ![Integrata con Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selezionare quindi il database con i dati di esempio **successivo**.

    ![Database di modifica predefinito](media/connect-odbc/change-default-database.png)

1. Nella schermata successiva, lasciare tutte le opzioni come valore predefinito è quindi selezionare **fine**.

1. Selezionare **origine dati di Test**.

    ![Verifica origine dati](media/connect-odbc/test-data-source.png)

1. Verificare che il test ha avuto esito positivo quindi selezionare **OK**. Se il test non è riuscito, controllare i valori specificati nei passaggi precedenti e assicurarsi di che disporre di autorizzazioni sufficienti per connettersi al cluster.

    ![Test ha avuto esito positivo](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi a Esplora dati di Azure da Tableau](tableau.md)