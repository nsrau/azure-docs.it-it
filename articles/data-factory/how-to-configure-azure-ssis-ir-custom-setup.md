---
title: Personalizzare l'installazione di Azure-SSIS Integration Runtime
description: Questo articolo descrive come usare l'interfaccia di installazione personalizzata per Azure-SSIS Integration Runtime per installare componenti aggiuntivi o modificare le impostazioni
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: d2a5928d8326c4a0628ebc1bfb7eec3cd20f9254
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747515"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizzare l'installazione di Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'installazione personalizzata per Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) offre un'interfaccia per l'aggiunta di passaggi di installazione durante l'installazione o la riconfigurazione di Azure-SSIS IR. 

Usando l'installazione personalizzata, è possibile modificare la configurazione operativa o l'ambiente predefinito ad esempio per avviare servizi Windows aggiuntivi, rendere permanenti le credenziali di accesso per le condivisioni file o usare la crittografia avanzata o un protocollo di rete più sicuro (TLS 1.2). In alternativa, è possibile installare componenti aggiuntivi, ad esempio assembly, driver o estensioni, in ogni nodo di Azure-SSIS IR.

È possibile eseguire installazioni personalizzate in Azure-SSIS IR in uno dei due modi seguenti: 
* **Impostazione rapida personalizzata senza uno script**: eseguire alcune configurazioni di sistema comuni e i comandi di Windows oppure installare alcuni componenti aggiuntivi popolari o consigliati senza usare alcuno script.
* **Installazione personalizzata standard con uno script**: preparare uno script e i relativi file associati e caricarli in un contenitore BLOB nell'account di archiviazione di Azure. Si specifica quindi l'URI (Uniform Resource Identifier) di una firma di accesso condiviso per il contenitore durante l'installazione o la riconfigurazione di Azure-SSIS IR. Ogni nodo di Azure-SSIS IR scarica quindi lo script e i file associati dal contenitore ed esegue l'installazione personalizzata con autorizzazioni elevate. Al termine dell'installazione personalizzata, ogni nodo carica l'output standard dell'esecuzione e gli altri log nel contenitore.

È possibile installare componenti gratuiti senza licenza e componenti a pagamento con licenza con l'installazione rapida e standard personalizzata. Se si è un fornitore di software indipendente (ISV), vedere [Sviluppare componenti a pagamento o con licenza per Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Per avere a disposizione i miglioramenti futuri, è consigliabile usare le serie di nodi v3 o successive per Azure-SSIS IR con l'installazione personalizzata.

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti si applicano solo alle installazioni personalizzate standard:

- Se si vuole usare *gacutil.exe* nello script per installare assembly in Global Assembly Cache (GAC), è necessario specificare *gacutil.exe* come parte dell'installazione personalizzata. In alternativa, è possibile usare la copia disponibile nel contenitore *Public Preview* (Anteprima pubblica), descritta più avanti nella sezione "Istruzioni".

- Se si vuole fare riferimento a una sottocartella nello script, *msiexec.exe* non supporta la notazione `.\` usata per i riferimenti alla cartella radice. Usare un comando, ad esempio `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`, anziché `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Le condivisioni amministrative o le condivisioni di rete nascoste create automaticamente da Windows non sono attualmente supportate in Azure-SSIS IR.

- Il driver ODBC IBM iSeries Access non è supportato in Azure-SSIS IR. Potrebbero essere visualizzati errori di installazione durante l'installazione personalizzata. In tal caso, contattare il supporto tecnico IBM per assistenza.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per personalizzare Azure-SSIS IR sono necessari gli elementi seguenti:

- [Una sottoscrizione di Azure](https://azure.microsoft.com/)

- [Eseguire il provisioning del runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un account di archiviazione di Azure](https://azure.microsoft.com/services/storage/). Non richiesto per le installazioni rapide personalizzate. Per le installazioni standard personalizzate, lo script di installazione personalizzato e i file associati vengono caricati e archiviati in un contenitore BLOB. Il processo di installazione personalizzata carica nello stesso contenitore BLOB anche il log di esecuzione.

## <a name="instructions"></a>Istruzioni

1. Se si vuole installare o riconfigurare Azure-SSIS IR con PowerShell, scaricare e installare [Azure PowerShell](/powershell/azure/install-az-ps). Per le installazioni rapide personalizzate, andare al passaggio 4.

1. Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

   * È necessario avere un file di script denominato *main.cmd*, che è il punto di ingresso dell'installazione personalizzata.  
   * Per assicurarsi che lo script possa essere eseguito in modalità invisibile all'utente, è consigliabile testarlo prima nel computer locale.  
   * Se si vuole caricare nel contenitore i log aggiuntivi generati da altri strumenti (ad esempio *msiexec.exe*), specificare negli script la variabile di ambiente predefinita `CUSTOM_SETUP_SCRIPT_LOG_DIR` come cartella dei log (ad esempio *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Scaricare, installare e aprire [Azure Storage Explorer](https://storageexplorer.com/). A tale scopo, procedere come indicato di seguito:

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Storage Accounts** (Account di archiviazione) e quindi selezionare **Connect to Azure storage** (Connetti ad archiviazione di Azure).

      ![Connettersi ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e quindi selezionare **Next** (Avanti).

      ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Immettere il nome dell'account di archiviazione di Azure e la chiave, selezionare **Next** (Avanti), quindi selezionare **Connect** (Connetti).

      ![Specificare il nome e la chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Nell'account di archiviazione di Azure connesso fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB), selezionare **Create Blob Container** (Crea contenitore BLOB) e assegnare un nome al nuovo contenitore.

      ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare *main.cmd* nel primo livello del contenitore, non in una cartella qualsiasi. Assicurarsi anche che il contenitore includa solo i file di installazione personalizzati necessari, in modo che il successivo download dei file in Azure-SSIS IR non richieda troppo tempo. La durata massima di un'installazione personalizzata è attualmente impostata su 45 minuti prima del timeout. È incluso il tempo necessario per scaricare tutti i file dal contenitore e installarli in Azure-SSIS IR. Se l'installazione richiede più tempo, generare un ticket di supporto.

      ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Fare clic con il pulsante destro del mouse sul contenitore e quindi selezionare **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

      ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Creare l'URI SAS per il contenitore con una scadenza sufficientemente lunga e con autorizzazioni di lettura, scrittura ed elenco. L'URI SAS è necessario per scaricare ed eseguire lo script di installazione personalizzato e i file associati ogni volta che viene ricreata l'immagine di un nodo di Azure-SSIS IR o il nodo viene riavviato. Le autorizzazioni di scrittura sono necessarie per caricare i log di esecuzione dell'installazione.

      > [!IMPORTANT]
      > Assicurarsi che l'URI SAS non scada e che le risorse del programma di installazione siano sempre disponibili durante l'intero ciclo di vita del runtime di integrazione Azure-SSIS, dalla creazione all'eliminazione, soprattutto se si arresta e si riavvia regolarmente il runtime di integrazione Azure-SSIS durante questo periodo.

      ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copiare e salvare l'URI SAS del contenitore.

      ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando si installa o si riconfigura Azure-SSIS IR con un'interfaccia utente di data factory, è possibile aggiungere o rimuovere le installazioni personalizzate selezionando la casella di controllo **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizza Azure-SSIS IR con configurazioni di sistema/installazioni di componenti personalizzati) nella sezione **Advanced Settings** (Impostazioni avanzate) del riquadro **Integration runtime setup** (Installazione runtime di integrazione). 

   Se si vuole aggiungere installazioni standard personalizzate, immettere l'URI SAS del contenitore nella casella **Custom setup container SAS URI** (URI SAS contenitore installazione personalizzata). 
   
   Se si vuole aggiungere installazioni rapide personalizzate, selezionare **New** (Nuovo) per aprire il riquadro **Add express custom setup** (Aggiungi installazione rapida personalizzata) e quindi selezionare un tipo nell'elenco a discesa **Express custom setup type** (Tipo di installazione rapida personalizzata):

   * Se si seleziona il tipo **Run cmdkey command**(Esegui comando cmdkey), è possibile rendere permanenti le credenziali di accesso per le condivisioni file o le condivisioni di File di Azure in Azure-SSIS IR immettendo il nome del computer o il nome di dominio di destinazione, il nome account o il nome utente e la chiave dell'account o la password nelle caselle **/Add** (Aggiungi), **/User** (Utente) e **/Pass** (Password). Questa operazione è simile all'esecuzione del comando di Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) nel computer locale.
   
   * Se si seleziona il tipo **Add environment variable** (Aggiungi variabile di ambiente), è possibile aggiungere le variabili di ambiente Windows da usare nei pacchetti in esecuzione in Azure-SSIS IR immettendo il nome della variabile di ambiente e il valore nelle caselle **Variable name** (Nome variabile) e **Variable value** (Valore variabile). Questa operazione è simile all'esecuzione del comando di Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) nel computer locale.

   * Se si seleziona il tipo **Install licensed component** (Installa componente con licenza), è possibile selezionare un componente integrato dei partner ISV nell'elenco a discesa **Component name** (Nome componente):

     * Se si seleziona il componente **SentryOne's Task Factory**, è possibile installare il gruppo di componenti [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) di SentryOne in Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato nella casella **License key** (Codice di licenza). La versione integrata corrente è **2019.4.3**.

     * Se si seleziona il componente **oh22's HEDDA.IO**, è possibile installare il componente di qualità/pulizia dati [HEDDA.IO](https://hedda.io/ssis-component/) di oh22 in Azure-SSIS IR dopo aver acquistato il servizio. La versione integrata corrente è **1.0.13**.

     * Se si seleziona il componente **oh22's SQLPhonetics.NET**, è possibile installare il componente di qualità/corrispondenza dati [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) di oh22 in Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato nella casella **License key** (Codice di licenza). La versione integrata corrente è **1.0.43**.

     * Se si seleziona il componente **KingswaySoft's SSIS Integration Toolkit**, è possibile installare il gruppo di connettori [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) per le app CRM, ERP, di marketing e di collaborazione, ad esempio Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, di KingswaySoft in Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato nella casella **License key** (Codice di licenza). La versione integrata corrente è **2019.2**.

     * Se si seleziona il componente **KingswaySoft's SSIS Productivity Pack**, è possibile installare il gruppo di componenti [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) di KingswaySoft in Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato nella casella **License key** (Codice di licenza). La versione integrata corrente è **10.0**.

     * Se si seleziona il componente **Theobald Software's Xtract IS**, è possibile installare il gruppo di connettori [Xtract IS](https://theobald-software.com/en/xtract-is/) per il sistema SAP (ERP, S/4HANA, BW) di Theobald Software in Azure-SSIS IR trascinando/caricando il file di licenza del prodotto acquistato nella casella **License file** (File licenza). La versione integrata corrente è **6.1.1.3**.

   Le installazioni rapide personalizzate aggiunte verranno visualizzate nella sezione **Advanced Settings** (Impostazioni avanzate). Per rimuoverle, selezionare le caselle di controllo corrispondenti e quindi selezionare **Delete** (Elimina).

   ![Impostazioni avanzate per le installazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando si installa o si riconfigura Azure-SSIS IR con PowerShell, è possibile aggiungere o rimuovere le installazioni personalizzate eseguendo il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` prima di avviare Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Al termine dell'installazione standard personalizzata e dopo l'avvio di Azure-SSIS IR, l'output standard di *main.cmd* e gli altri log di esecuzione sono disponibili nella cartella *main.cmd.log* del contenitore di archiviazione.

1. Per visualizzare alcuni esempi di installazioni standard personalizzate, connettersi al contenitore Public Preview (Anteprima pubblica) usando Azure Storage Explorer.

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

      ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selezionare **Use a SAS URI** (Usa un URI SAS) e quindi nella casella **URI** immettere l'URI SAS seguente:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selezionare **Next** (Avanti) e quindi **Connect** (Connetti).

   d. Nel riquadro sinistro selezionare il contenitore **publicpreview** connesso, quindi fare doppio clic sulla cartella *CustomSetupScript*. Questa cartella contiene gli elementi seguenti:

      * Una cartella *Sample* che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo di Azure-SSIS IR. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene anche una cartella *gacutil* di cui è possibile copiare l'intero contenuto (*gacutil.exe*, *gacutil.exe.config* e *1033\gacutlrc.dll*) nel contenitore.

      * Una cartella *UserScenarios* che contiene diversi esempi di installazioni personalizzate di scenari utente reali.

        ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Fare doppio clic sulla cartella *UserScenarios* per trovare gli elementi seguenti:

      * Una cartella *.NET FRAMEWORK 3.5* che contiene uno script di installazione personalizzato (*main.cmd*) per installare una versione precedente di .NET Framework che potrebbe essere necessaria per i componenti personalizzati in ogni nodo di Azure-SSIS IR.

      * Una cartella *BCP* che contiene uno script di installazione personalizzato (*main.cmd*) per installare utilità della riga di comando di SQL Server (*MsSqlCmdLnUtils.msi*), tra cui il programma di copia bulk (*bcp*), in ogni nodo di Azure-SSIS IR.

      * Una cartella *EXCEL* che contiene uno script di installazione personalizzato (*main.cmd*) per installare assembly e librerie C# che è possibile usare nelle attività di script per leggere e scrivere in modo dinamico i file di Excel in ogni nodo di Azure-SSIS IR. 
      
        Innanzitutto, scaricare [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) e quindi caricarli insieme a *main.cmd* nel contenitore. In alternativa, se si vuole usare solo la gestione connessione di Excel standard, l'origine Excel e la destinazione Excel, il file ridistribuibile di accesso richiesto è già preinstallato in Azure-SSIS IR e pertanto non è necessaria alcuna installazione personalizzata.
      
      * Una cartella *MYSQL ODBC* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC di MySQL in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ODBC per connettersi al server MySQL. 
     
        Innanzitutto, [scaricare le versioni a 64 bit e a 32 bit più recenti dei programmi di installazione dei driver ODBC di MySQL](https://dev.mysql.com/downloads/connector/odbc/) (ad esempio *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*) e quindi caricarle insieme a *main.cmd* nel contenitore.

      * Una cartella *ORACLE ENTERPRISE* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di configurazione per l'installazione invisibile all'utente (*client.rsp*) dei connettori e del driver OCI di Oracle in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Oracle per connettersi al server Oracle. 
      
        Innanzitutto, scaricare Microsoft Connectors v5.0 for Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e il client Oracle più recente (ad esempio *winx64_12102_client.zip*) da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) e quindi caricarli insieme a *main.cmd* e *client.rsp* nel contenitore. Se si usa TNS per la connessione a Oracle, è necessario anche scaricare *tnsnames.ora*, modificarlo e caricarlo nel contenitore in modo che possa essere copiato nella cartella di installazione di Oracle durante l'installazione.

      * Una cartella *ORACLE STANDARD ADO.NET* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver ODP.NET di Oracle in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ADO.NET per connettersi al server Oracle. 
      
        Innanzitutto, [scaricare il driver ODP.NET di Oracle più recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (ad esempio *ODP.NET_Managed_ODAC122cR1.zip*) e quindi caricarlo insieme a *main.cmd* nel contenitore.
       
      * Una cartella *ORACLE STANDARD ODBC* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver ODBC di Oracle e configurare il nome dell'origine dati (DSN) in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ODBC o la gestione connessione e l'origine di Power Query con il tipo di origine dati ODBC per connettersi al server Oracle. 
      
        Innanzitutto, scaricare la versione più recente di Oracle Instant Client (Basic Package o Basic Lite Package) e il pacchetto ODBC e quindi caricarli insieme a *main.cmd* nel contenitore:
        * [Scaricare i pacchetti a 64 bit](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic Package: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite Package: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Scaricare i pacchetti a 32 bit](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite Package: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Una cartella *ORACLE STANDARD OLEDB* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver OLEDB di Oracle in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione OLEDB per connettersi al server Oracle. 
     
        Innanzitutto, [scaricare il driver OLEDB di Oracle più recente](https://www.oracle.com/partners/campaign/index-090165.html) (ad esempio *ODAC122010Xcopy_x64.zip*) e quindi caricarlo insieme a *main.cmd* nel contenitore.

      * Una cartella *POSTGRESQL ODBC* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC di PostgreSQL in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ODBC per connettersi al server PostgreSQL. 
     
        Innanzitutto, [scaricare le versioni a 64 bit e a 32 bit più recenti dei programmi di installazione dei driver ODBC di PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (ad esempio *psqlodbc_x64.msi* e *psqlodbc_x86.msi*) e quindi caricarle insieme a *main.cmd* nel contenitore.

      * Una cartella *SAP BW* che contiene uno script di installazione personalizzato (*main.cmd*) per installare l'assembly SAP .NET Connector (*librfc32.dll*) in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione di SAP Business Warehouse (BW) per connettersi al server SAP BW. 
      
        Innanzitutto, caricare la versione a 64 bit o a 32 bit di *librfc32.dll* dalla cartella di installazione SAP insieme a *main.cmd* nel contenitore. Lo script copia quindi l'assembly SAP nella cartella *%windir%\SysWow64* o *%windir%\System32* durante l'installazione.

      * Una cartella *STORAGE* che contiene uno script di installazione personalizzato (*main.cmd*) per installare Azure PowerShell in ogni nodo di Azure-SSIS IR. Questa installazione consente di distribuire ed eseguire i pacchetti SSIS che eseguono gli [script di PowerShell per la gestione dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copiare *main.cmd*, *AzurePowerShell. msi* di esempio (oppure usare la versione più recente) e *storage.ps1* nel contenitore. Usare *PowerShell.dtsx* come modello per i pacchetti. Il modello di pacchetto combina un'[attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), che scarica *storage.ps1* come script di PowerShell modificabile, e un'[attività Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) che esegue lo script in ogni nodo.

      * Una cartella *TERADATA* che contiene uno script di installazione personalizzato (*main.cmd*), il file associato (*install.cmd*) e i pacchetti di installazione ( *.msi*). Questi file installano i connettori Teradata, l'API TPT (Teradata Parallel Transporter) e il driver ODBC in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Teradata per connettersi al server Teradata. 
      
        Innanzitutto, [scaricare il file ZIP di Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (ad esempio *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) e quindi caricarlo insieme ai file *.cmd* e *.msi* indicati in precedenza nel contenitore.

      * Una cartella *TLS 1.2* che contiene uno script di installazione personalizzato (*main.cmd*) per usare il protocollo di rete con crittografia avanzata più sicuro (TLS 1.2) e disabilitare le versioni precedenti di SSL/TLS in ogni nodo di Azure-SSIS IR.

      * Una cartella *ZULU OPENJDK* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di PowerShell (*install_openjdk.ps1*) per installare Zulu OpenJDK in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare Azure Data Lake Store e connettori File flessibili per elaborare i file ORC e Parquet. Per altre informazioni, vedere [Feature Pack di Azure per Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Innanzitutto, [scaricare la versione più recente di Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (ad esempio *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) e quindi caricarla insieme a *main.cmd* e *install_openjdk.ps1* nel contenitore.

        ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Per provare questi esempi di installazione personalizzata, copiare il contenuto dalla cartella selezionata nel contenitore.
   
      Quando si installa o si riconfigura Azure-SSIS IR con l'interfaccia utente di Data Factory, selezionare la casella di controllo **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizza Azure-SSIS IR con configurazioni di sistema/installazioni di componenti personalizzati) nella sezione **Advanced Settings** (Impostazioni avanzate) e quindi immettere l'URI SAS del contenitore nella casella **Custom setup container SAS URI** (URI SAS contenitore installazione personalizzata).
   
      Quando si installa o si riconfigura Azure-SSIS IR con PowerShell, eseguire il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` con l'URI SAS del contenitore come valore per il parametro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Passaggi successivi

- [Installare la versione Enterprise Edition di Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Sviluppare componenti personalizzati a pagamento o concessi in licenza per Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
