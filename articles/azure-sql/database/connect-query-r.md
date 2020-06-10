---
title: Usare R con Machine Learning Services nel database SQL di Azure (anteprima) per eseguire query su un database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Questo articolo illustra come usare uno script R con Machine Learning Services del database SQL di Azure per connettersi a un database nel database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7bfc00566ef24fb6d6851a7cdee402598fe15951
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84052618"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Avvio rapido: Usare R con Machine Learning Services nel database SQL di Azure (anteprima) per eseguire query su un database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo argomento di avvio rapido illustra come usare R con Machine Learning Services nel database SQL di Azure per connettersi a un database nel database SQL di Azure e usare le istruzioni T-SQL per eseguire query sui dati.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Database SQL di Azure](single-database-create-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) con R abilitato.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Gli script di questo articolo sono scritti in modo da usare il database **Adventure Works**.

Machine Learning Services con R è una funzionalità di Database SQL di Azure usata per l'esecuzione di script R nel database. Per altre informazioni, vedere il [progetto R](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>Ottenere le informazioni di connessione per SQL Server

Recuperare le informazioni di connessione necessarie per connettersi al database nel database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per il database nel database SQL di Azure o il nome del server completo accanto a **Host** per un'istanza gestita in Istanza gestita di SQL di Azure. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

## <a name="create-code-to-query-your-database"></a>Creare il codice per eseguire query sul database

1. Aprire **SQL Server Management Studio** e connettersi al database.

   Per assistenza con la connessione, vedere [Avvio rapido: Usare SQL Server Management Studio per connettersi a un database nel database SQL di Azure ed eseguire query](connect-query-ssms.md).

1. Passare lo script R completo alla stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Lo script viene passato tramite l'argomento `@script`. Tutti gli elementi all'interno dell'argomento `@script` devono essere costituiti da codice R valido.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se si verificano errori, è possibile che l'anteprima pubblica di Machine Learning Services (con R) non sia abilitata per il database in uso. Vedere i [prerequisiti](#prerequisites) riportati sopra.

## <a name="run-the-code"></a>Eseguire il codice

1. Eseguire la stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Verificare che vengano restituite le prime 20 righe di categoria/prodotto nella finestra dei **messaggi**.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database nel database SQL di Azure](design-first-database-tutorial.md)
- [Machine Learning Services (con R) nel database SQL di Azure](machine-learning-services-overview.md)
- [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](r-script-create-quickstart.md) Creare ed eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima)
- [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](machine-learning-services-functions.md)
