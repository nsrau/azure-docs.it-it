---
title: Connettersi ad Azure Esplora dati con ODBC
description: Questo articolo illustra come configurare una connessione ODBC (Open Database Connectivity) a Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034024"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Connettersi ad Azure Esplora dati con ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) è un'Application Programming Interface (API) ampiamente accettata per l'accesso al database. Usare ODBC per connettersi ad Azure Esplora dati da applicazioni che non dispongono di un connettore dedicato.

In background, le applicazioni chiamano funzioni nell'interfaccia ODBC, implementate in moduli specifici del database denominati *driver*. Azure Esplora dati supporta un subset del protocollo di comunicazione SQL Server ([MS-TDS](/azure/kusto/api/tds/)), in modo da poter utilizzare il driver ODBC per SQL Server.

Utilizzando il video seguente, è possibile apprendere come creare una connessione ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

In alternativa, è possibile [configurare l'origine dati ODBC](#configure-the-odbc-data-source) come descritto di seguito. 

In questo articolo viene illustrato come utilizzare il driver ODBC di SQL Server, in modo da potersi connettere ad Azure Esplora dati da qualsiasi applicazione che supporti ODBC. 

## <a name="prerequisites"></a>prerequisiti

Sono necessari gli elementi seguenti:

* [Microsoft ODBC driver for SQL Server versione 17.2.0.1 o successiva](/sql/connect/odbc/download-odbc-driver-for-sql-server) per il sistema operativo in uso.

## <a name="configure-the-odbc-data-source"></a>Configurare l'origine dati ODBC

Attenersi alla procedura seguente per configurare un'origine dati ODBC utilizzando il driver ODBC per SQL Server.

1. In Windows cercare le *origini dati ODBC*e aprire l'app desktop origini dati ODBC.

1. Selezionare **Aggiungi**.

    ![Aggiungere l'origine dati](media/connect-odbc/add-data-source.png)

1. Selezionare **ODBC driver 17 per SQL Server** quindi **fine**.

    ![Seleziona driver](media/connect-odbc/select-driver.png)

1. Immettere un nome e una descrizione per la connessione e il cluster a cui si vuole connettersi, quindi fare clic su **Avanti**. Il formato dell'URL del cluster deve essere *\<clustername\>.\<Region\>. kusto.Windows.NET*.

    ![Selezione server](media/connect-odbc/select-server.png)

1. Selezionare **Active Directory integrato** quindi **Avanti**.

    ![Active Directory integrata](media/connect-odbc/active-directory-integrated.png)

1. Selezionare il database con i dati di esempio **successivamente.**

    ![Modificare il database predefinito](media/connect-odbc/change-default-database.png)

1. Nella schermata successiva lasciare tutte le opzioni predefinite e quindi fare clic su **fine**.

1. Selezionare **Test origine dati**.

    ![Origine dati di test](media/connect-odbc/test-data-source.png)

1. Verificare che il test sia stato completato e quindi fare clic su **OK**. Se il test non ha avuto esito positivo, controllare i valori specificati nei passaggi precedenti e assicurarsi di disporre di autorizzazioni sufficienti per connettersi al cluster.

    ![Test completato](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ad Azure Esplora dati da Tableau](tableau.md)