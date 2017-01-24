---
title: Introduzione al catalogo di U-SQL di Azure Data Lake Analytics | Documentazione Microsoft
description: Introduzione al catalogo di U-SQL di Azure Data Lake Analytics
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 55fef96f-e941-4d09-af5e-dd7c88c502b2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 64427583dba57716c23e1a01df820a263c5e7298


---
# <a name="use-azure-data-lake-analytics-u-sql-catalog"></a>Usare il catalogo di Azure Data Lake Analytics (U-SQL)
Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake.

A ogni account di Azure Data Lake Analytics è associato un unico catalogo di U-SQL. Non è possibile eliminare il catalogo di U-SQL. Attualmente i cataloghi di U-SQL non possono essere condivisi tra account di Data Lake Store.

Ogni catalogo di U-SQL contiene un database denominato **Master**. Il database master non può essere eliminato.  Ogni catalogo di U-SQL può contenere database aggiuntivi.

Il database U-SQL contiene:

* Assembly: consentono di condividere il codice .NET tra script U-SQL.
* Funzioni di valori di tabella: consentono di condividere il codice U-SQL tra script U-SQL.
* Tabelle: consentono di condividere dati tra script U-SQL.
* Schemi: consentono di condividere schemi di tabella tra script U-SQL.

## <a name="manage-catalogs"></a>Gestire i cataloghi
A ogni account di Azure Data Lake Analytics è associato un account di Azure Data Lake Store predefinito. Questo account di Data Lake Store viene definito come account di Data Lake Store predefinito. Il catalogo di U-SQL viene archiviato nell'account di Data Lake Store predefinito nella cartella /catalog. Non eliminare i file contenuti nella cartella /catalog.

### <a name="use-azure-portal"></a>Usare il portale di Azure
Vedere [Gestire Data Lake Analytics mediante il portale](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)

### <a name="use-data-lake-tools-for-visual-studio"></a>Usare Strumenti di Data Lake per Visual Studio
È possibile utilizzare Strumenti di Data Lake per Visual Studio per gestire il catalogo.  Per altre informazioni sugli strumenti, vedere [Uso di Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Per gestire il catalogo**

1. Aprire Visual Studio e connettersi ad Azure. Per istruzioni, vedere [Connettersi ad Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
2. Aprire **Esplora server** premendo **CTRL+ALT+S**.
3. Da **Esplora server**, espandere **Azure**, quindi **Data Lake Analytics**, l'account Data Lake Analytics in uso, **Database** e infine **master**.

    - Per aggiungere un nuovo database, fare clic con il pulsante destro del mouse su **Database** e quindi scegliere **Crea database**.
    - Per aggiungere un nuovo assembly, fare clic con il pulsante destro del mouse su **Assembly** e quindi scegliere **Registra assembly**.
    - Per aggiungere un nuovo schema, fare clic con il pulsante destro del mouse su **Schemi** e quindi scegliere **Crea schema**.
    - Per aggiungere una nuova tabella, fare clic con il pulsante destro del mouse su **Tabella** e quindi scegliere **Crea tabella**.
    - Per aggiungere una nuova funzione con valori di tabella, vedere [Sviluppare operatori U-SQL definiti dall'utente per i processi di Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Sfogliare i cataloghi Visual Studio di U-SQL](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)

## <a name="see-also"></a>Vedere anche
* Introduzione
  
  * [Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
  * [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Introduzione ad Azure Data Lake Analytics con Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
  * [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* U-SQL e sviluppo
  
  * [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
  * [Usare le funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
  * [Sviluppare operatori U-SQL definiti dall'utente per i processi di Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* gestione
  
  * [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
  * [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
* Esercitazione end-to-end
  
  * [Usare le esercitazioni interattive di Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
  * [Analizzare i log dei siti Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)




<!--HONumber=Dec16_HO2-->


