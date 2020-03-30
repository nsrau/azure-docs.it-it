---
title: Connettersi ad Azure Data Explorer con ODBCConnect to Azure Data Explorer with ODBC
description: In this article, you learn how to set up an Open Database Connectivity (ODBC) connection to Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034024"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Connettersi ad Azure Data Explorer con ODBCConnect to Azure Data Explorer with ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) è un'API (Application Programming Interface) ampiamente accettata per l'accesso al database. Usare ODBC per connettersi ad Azure Data Explorer da applicazioni che non dispongono di un connettore dedicato.

Dietro le quinte, le applicazioni chiamano funzioni nell'interfaccia ODBC, che vengono implementate in moduli specifici del database *denominati driver*. Azure Data Explorer supporta un sottoinsieme del protocollo di comunicazione di SQL Server ([MS-TDS](/azure/kusto/api/tds/)), in modo che possa usare il driver ODBC per SQL Server.

Utilizzando il video seguente, è possibile imparare a creare una connessione ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

In alternativa, è possibile [configurare l'origine dati ODBC](#configure-the-odbc-data-source) come descritto di seguito. 

In questo articolo viene illustrato come utilizzare il driver ODBC di SQL Server, in modo da potersi connettere ad Azure Data Explorer da qualsiasi applicazione che supporta ODBC. 

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

* [Driver Microsoft ODBC per SQL Server versione 17.2.0.1 o successiva](/sql/connect/odbc/download-odbc-driver-for-sql-server) per il sistema operativo in uso.

## <a name="configure-the-odbc-data-source"></a>Configurare l'origine dati ODBC

Seguire questi passaggi per configurare un'origine dati ODBC utilizzando il driver ODBC per SQL Server.

1. In Windows cercare *Origini dati ODBC*e aprire l'app desktop Origini dati ODBC.

1. Selezionare **Aggiungi**.

    ![Aggiungere l'origine dati](media/connect-odbc/add-data-source.png)

1. Selezionare **Driver ODBC 17 per SQL Server,** quindi **Fine**.

    ![Seleziona driver](media/connect-odbc/select-driver.png)

1. Immettere un nome e una descrizione per la connessione e il cluster a cui si desidera connettersi, quindi selezionare **Avanti**. L'URL del cluster deve essere nel formato * \<NomeCluster\>.\< Regione\>.kusto.windows.net .*

    ![Seleziona server](media/connect-odbc/select-server.png)

1. Selezionare **Integrato in Active Directory,** quindi **Avanti**.

    ![Integrato in Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selezionare il database con i dati di esempio, quindi **Avanti**.

    ![Modificare il database predefinito](media/connect-odbc/change-default-database.png)

1. Nella schermata successiva, lasciare tutte le opzioni come predefinite, quindi selezionare **Fine**.

1. Selezionare **Origine dati di test**.

    ![Testare l'origine dati](media/connect-odbc/test-data-source.png)

1. Verificare che il test sia riuscito, quindi selezionare **OK**. Se il test non ha avuto esito positivo, controllare i valori specificati nei passaggi precedenti e verificare di disporre di autorizzazioni sufficienti per connettersi al cluster.

    ![Test riuscito](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ad Azure Data Explorer da TableauConnect to Azure Data Explorer from Tableau](tableau.md)