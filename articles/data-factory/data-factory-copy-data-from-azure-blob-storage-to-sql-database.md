<properties
    pageTitle="Copiare dati da un archivio BLOB al database SQL | Microsoft Azure"
    description="Questa esercitazione illustra come usare l'attività di copia in una pipeline di Azure Data Factory per copiare i dati da un archivio BLOB a un database SQL."
    Keywords="blob sql, archivio blob, copia di dati"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>


# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copiare dati da un archivio BLOB al database SQL usando Data Factory 
> [AZURE.SELECTOR]
- [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


In questa esercitazione si crea una data factory con una pipeline per copiare i dati dall'archivio BLOB al database SQL.

L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .  

> [AZURE.NOTE] Per una panoramica dettagliata del servizio Data factory, vedere l'articolo [Introduzione al servizio Azure Data Factory](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Prerequisiti per l'esercitazione
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Sottoscrizione di Azure**.  Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere l'articolo [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/) .
- **Account di archiviazione di Azure**. In questa esercitazione l'archivio BLOB viene usato come archivio dati di **origine** . Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
- **Database SQL di Azure**. In questa esercitazione viene usato un database SQL di Azure come archivio dati di **destinazione** . Se non è disponibile un database SQL di Azure da usare nell'esercitazione, vedere [Come creare e configurare un database SQL di Azure](../sql-database/sql-database-get-started.md) per crearne uno.
- **SQL Server 2012/2014 o Visual Studio 2013**. Per creare un database di esempio e per visualizzare i dati risultanti in tale database, viene usato SQL Server Management Studio o Visual Studio.  

## <a name="collect-blob-storage-account-name-and-key"></a>Raccogliere il nome dell'account e la chiave dell'archivio BLOB 
Per eseguire questa esercitazione, sono necessari il nome e la chiave dell'account di archiviazione di Azure. Prendere nota del **nome** e della **chiave** per l'account di archiviazione di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Fare clic su **Altri servizi** nel menu a sinistra e selezionare **Account di archiviazione**.

    ![Sfoglia - Account di archiviazione](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. Nel pannello **Account di archiviazione** selezionare l'**account di archiviazione di Azure** da usare in questa esercitazione.
4. Selezionare **Chiavi di accesso** in **IMPOSTAZIONI**.
5.  Fare clic sul pulsante **copia** (immagine) accanto alla casella di testo **Nome account di archiviazione** e salvarlo/incollarlo, ad esempio, in un file di testo.
6. Ripetere il passaggio precedente per copiare o annotare la chiave denominata **key1**.
    
    ![Chiave di accesso alle risorse di archiviazione](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Fare clic su **X**per chiudere tutti i pannelli.

## <a name="collect-sql-server,-database,-user-names"></a>Raccogliere i nomi del server, del database e dell'utente per il database SQL
Per eseguire questa esercitazione, sono necessari i nomi del server, del database e dell'utente di Azure SQL. Annotare i nomi di **server**, **database** e **utente** per il database SQL di Azure.

1. Nel **portale di Azure** fare clic su **Altri servizi** a sinistra e selezionare **Database SQL**.
2. Nel pannello **Database SQL** selezionare il **database** da usare nell'esercitazione. Annotare il **nome database**.  
3. Nel pannello **Database SQL**, fare clic su **Proprietà** in **IMPOSTAZIONI**.
4. Annotare i valori per **NOME SERVER** e **ACCOUNT DI ACCESSO AMMINISTRATORE SERVER**.
5. Fare clic su **X**per chiudere tutti i pannelli.

## <a name="allow-azure-services-to-access-sql-server"></a>Consentire ai servizi di Azure di accedere a SQL Server 
Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ATTIVA** per il server di Azure SQL, in modo che il servizio Data Factory possa accedere al server di Azure SQL. Per verificare e attivare l'impostazione, seguire questa procedura:

1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**. 
4. Nel pannello **Impostazioni del firewall** fare clic su **ATTIVA** per **Consenti l'accesso a Servizi Azure**.
5. Fare clic su **X**per chiudere tutti i pannelli.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparare l'archivio BLOB e il database SQL 
Preparare ora l'archivio BLOB di Azure e il database SQL Azure per l'esercitazione seguendo questa procedura:  

1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\ADFGetStarted** sul disco rigido.

        John, Doe
        Jane, Doe

2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Azure Storage Explorer Copiare dati da un archivio BLOB al database SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Se nel computer è installato SQL Server 2012/2014:** seguire le istruzioni riportate nel [Passaggio 2: Connettersi al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) per connettersi al server SQL di Azure ed eseguire lo script SQL. Per configurare il firewall per un server SQL di Azure, questo articolo usa il [portale di Azure classico](http://manage.windowsazure.com), non il [nuovo portale di Azure](https://portal.azure.com).

    Se il client non è autorizzato ad accedere al server di Azure SQL, è necessario configurare il firewall per il server di Azure SQL in modo da consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura per configurare il firewall per il server Azure SQL, vedere [questo articolo](../sql-database/sql-database-configure-firewall-settings.md) .

I passaggi relativi ai prerequisiti sono stati completati. È possibile creare un data factory usando uno dei seguenti metodi. Fare clic su una delle schede in alto oppure selezionare i collegamenti seguenti per eseguire l'esercitazione.     

- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)



<!--HONumber=Oct16_HO2-->


