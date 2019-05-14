---
title: Usare R per eseguire query su un database SQL di Azure
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Questo articolo illustra come usare uno script R per la connessione a un database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 2b1206e3087b0573736174d4eed502653d76f7a5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001187"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Avvio rapido: Usare R per eseguire query su un database SQL di Azure (anteprima)

 Questo Avvio rapido illustra come usare [R](https://www.r-project.org/) con Machine Learning Services per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati. Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. Per altre informazioni, vedere [Machine Learning Services (con R) nel database SQL di Azure](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurare | [Regola del firewall per gli indirizzi IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |||

- Machine Learning Services (con R) abilitati. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- La versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo Avvio rapido si userà SQL Server Management Studio.

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

## <a name="create-code-to-query-your-sql-database"></a>Creare codice per eseguire query sul database SQL

1. Aprire **SQL Server Management Studio** e connettersi al database SQL.

   Per assistenza con la connessione, vedere [Avvio rapido: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query](sql-database-connect-query-ssms.md).

1. Passare lo script R completo alla stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Lo script viene passato tramite l'argomento `@script`. Tutto il contenuto all'interno dell'argomento `@script` deve essere codice R valido.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se si verificano errori, è possibile che l'anteprima pubblica di Machine Learning Services (con R) non sia abilitata per il database SQL in uso. Vedere i [prerequisiti](#prerequisites) riportati sopra.

## <a name="run-the-code"></a>Eseguire il codice

1. Eseguire la stored procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Verificare che vengano restituite le prime 20 righe di categoria/prodotto nella finestra dei **messaggi**.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Machine Learning Services (con R) nel database SQL di Azure](sql-database-machine-learning-services-overview.md)
- [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md) Creare ed eseguire script R semplici nei Machine Learning Services nel database SQL di Azure (anteprima)
- [Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)](sql-database-machine-learning-services-functions.md)
