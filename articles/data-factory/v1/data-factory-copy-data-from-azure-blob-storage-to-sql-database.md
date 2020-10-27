---
title: Copiare dati da un archivio BLOB al database SQL-Azure
description: Questa esercitazione illustra come usare l'attività di copia in una pipeline di Azure Data Factory per copiare i dati da un archivio BLOB a un database SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 15bce219b96268124729de2f475e33fc386348a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631734"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Esercitazione: Copiare dati da un archivio BLOB al database SQL usando Data Factory
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere l'[esercitazione sull'attività di copia](../quickstart-create-data-factory-dot-net.md).

In questa esercitazione viene creato un data factory con una pipeline per copiare i dati dall'archivio BLOB al database SQL.

L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .  

> [!NOTE]
> Per una panoramica dettagliata del servizio Data factory, vedere l'articolo [Introduzione al servizio Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Prerequisiti per l'esercitazione
Prima di iniziare questa esercitazione, sono necessari i prerequisiti seguenti:

* **Sottoscrizione di Azure** .  Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere l'articolo [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) .
* **Account di archiviazione di Azure** . In questa esercitazione l'archivio BLOB viene usato come archivio dati di **origine** . Se non si ha un account di archiviazione di Azure, vedere l'articolo [creare un account di archiviazione](../../storage/common/storage-account-create.md) per i passaggi per crearne uno.
* **Database SQL di Azure** . In questa esercitazione si usa il database SQL di Azure come archivio dati di **destinazione** . Se non si dispone di un database nel database SQL di Azure che è possibile usare nell'esercitazione, vedere [come creare e configurare un database nel database SQL di Azure](../../azure-sql/database/single-database-create-quickstart.md) per crearne uno.
* **SQL Server 2012/2014 o Visual Studio 2013** . Per creare un database di esempio e per visualizzare i dati risultanti in tale database, viene usato SQL Server Management Studio o Visual Studio.  

## <a name="collect-blob-storage-account-name-and-key"></a>Raccogliere il nome dell'account e la chiave dell'archivio BLOB
Per eseguire questa esercitazione, sono necessari il nome e la chiave dell'account di archiviazione di Azure. Prendere nota del **nome** e della **chiave** per l'account di archiviazione di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Tutti i servizi** dal menu a sinistra e selezionare **Account di archiviazione** .

    ![Sfoglia - Account di archiviazione](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Nel pannello **Account di archiviazione** selezionare l' **account di archiviazione di Azure** da usare in questa esercitazione.
4. Selezionare **Chiavi di accesso** in **IMPOSTAZIONI** .
5. Fare clic sul pulsante **copia** (immagine) accanto alla casella di testo **Nome account di archiviazione** e salvarlo/incollarlo, ad esempio, in un file di testo.
6. Ripetere il passaggio precedente per copiare o annotare la chiave denominata **key1** .

    ![Chiave di accesso alle risorse di archiviazione](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Fare clic su **X** per chiudere tutti i pannelli.

## <a name="collect-sql-server-database-user-names"></a>Raccogliere i nomi del server, del database e dell'utente per il database SQL
Per eseguire questa esercitazione, sono necessari i nomi di SQL Server logico, database e utente. Prendere nota dei nomi del **Server** , del **database** e **dell'utente** per il database SQL di Azure.

1. Nel **portale di Azure** fare clic su **Tutti i servizi** a sinistra e selezionare **Database SQL** .
2. Nel pannello **Database SQL** selezionare il **database** da usare nell'esercitazione. Annotare il **nome database** .  
3. Nel pannello **Database SQL** , fare clic su **Proprietà** in **IMPOSTAZIONI** .
4. Annotare i valori per **NOME SERVER** e **ACCOUNT DI ACCESSO AMMINISTRATORE SERVER** .
5. Fare clic su **X** per chiudere tutti i pannelli.

## <a name="allow-azure-services-to-access-sql-server"></a>Consentire ai servizi di Azure di accedere a SQL Server
Assicurarsi che l'impostazione **Consenti l'accesso a servizi di Azure** sia attivata per il server **in** modo che il servizio Data Factory possa accedere al server. Per verificare e attivare l'impostazione, seguire questa procedura:

1. Fare clic sull'hub **Tutti i servizi** a sinistra e selezionare **Server SQL** .
2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI** .
3. Nel pannello **Impostazioni del firewall** fare clic su **ATTIVA** per **Consenti l'accesso a Servizi Azure** .
4. Fare clic su **X** per chiudere tutti i pannelli.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparare l'archivio BLOB e il database SQL
Preparare ora l'archivio BLOB di Azure e il database SQL di Azure per l'esercitazione attenendosi alla procedura seguente:  

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\ADFGetStarted** nel disco rigido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Usare strumenti come [Azure Storage Explorer](https://storageexplorer.com/) per creare il contenitore **adftutorial** e caricare il file di **emp.txt** nel contenitore.

3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Se nel computer è installato SQL Server 2012/2014:** seguire le istruzioni della pagina relativa alla [gestione del database SQL di Azure con SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) per connettersi al server ed eseguire lo script SQL.

    Se il client non è autorizzato ad accedere al server SQL logico, è necessario configurare il firewall per il server per consentire l'accesso dal computer (indirizzo IP). Vedere [questo articolo](../../azure-sql/database/firewall-configure.md) per i passaggi necessari per configurare il firewall per il server.

## <a name="create-a-data-factory"></a>Creare una data factory
I passaggi relativi ai prerequisiti sono stati completati. È possibile creare un data factory usando uno dei seguenti metodi. Per eseguire l'esercitazione, fare clic su una delle opzioni nell'elenco a discesa in alto oppure sui collegamenti indicati qui di seguito.     

* [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Non trasforma i dati di input per generare dati di output. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare la prima pipeline per elaborare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md).
>
> È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Per informazioni dettagliate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).