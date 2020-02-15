---
title: Personalizzare il programma di installazione di un Azure-SSIS Integration Runtime
description: Questo articolo descrive come usare l'interfaccia di installazione personalizzata per un Azure-SSIS Integration Runtime per installare componenti aggiuntivi o modificare le impostazioni
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
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251975"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizzare il programma di installazione di un Azure-SSIS Integration Runtime

Il programma di installazione personalizzato per un Integration Runtime di SQL Server Integration Services di Azure (Azure-SSIS IR) fornisce un'interfaccia per l'aggiunta di passaggi personalizzati durante l'installazione o la riconfigurazione del Azure-SSIS IR. 

Utilizzando l'installazione personalizzata, è possibile modificare la configurazione operativa o l'ambiente predefinito in, ad esempio, avviare servizi Windows aggiuntivi o salvare in modo permanente le credenziali di accesso per le condivisioni file. In alternativa, è possibile installare componenti aggiuntivi, ad esempio assembly, driver o estensioni, in ogni nodo della Azure-SSIS IR.

È possibile eseguire configurazioni personalizzate sul Azure-SSIS IR in uno dei due modi seguenti: 
* **Impostazione rapida personalizzata senza uno script**: eseguire alcune configurazioni di sistema comuni e i comandi di Windows oppure installare alcuni componenti aggiuntivi popolari o consigliati senza usare script.
* **Installazione personalizzata standard con uno script**: preparare uno script e i file associati e caricarli tutti insieme in un contenitore BLOB nell'account di archiviazione di Azure. Viene quindi fornito un Uniform Resource Identifier di firma di accesso condiviso (SAS) per il contenitore durante la configurazione o la riconfigurazione del Azure-SSIS IR. Ogni nodo del Azure-SSIS IR Scarica quindi lo script e i file associati dal contenitore ed esegue l'installazione personalizzata con autorizzazioni elevate. Al termine dell'installazione personalizzata, ogni nodo caricherà l'output standard di esecuzione e altri log nel contenitore.

È possibile installare i componenti gratuiti, senza licenza e i componenti a pagamento e con licenza con le configurazioni personalizzate Express e standard. Se si è un fornitore di software indipendente (ISV), vedere [sviluppare componenti a pagamento o concessi in licenza per un Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Poiché i nodi della serie V2 di un Azure-SSIS IR non sono adatti per l'installazione personalizzata, usare invece i nodi della serie V3. Se si usano già i nodi della serie V2, passare a nodi della serie V3 il prima possibile.

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti si applicano solo alle configurazioni personalizzate standard:

- Se si desidera utilizzare *gacutil. exe* nello script per installare gli assembly nella global assembly cache (GAC), è necessario specificare *gacutil. exe* come parte dell'installazione personalizzata. In alternativa, è possibile usare la copia fornita nel contenitore di *anteprima pubblica* , descritta più avanti nella sezione "istruzioni".

- Se si desidera fare riferimento a una sottocartella nello script, *msiexec. exe* non supporta la notazione `.\` per fare riferimento alla cartella radice. Usare un comando come `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` anziché `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Le condivisioni amministrative, o le condivisioni di rete nascoste create automaticamente da Windows, non sono attualmente supportate nel Azure-SSIS IR.

- Il driver ODBC di IBM iSeries Access non è supportato nel Azure-SSIS IR. Potrebbero essere visualizzati errori di installazione durante l'installazione personalizzata. In tal caso, contattare il supporto tecnico IBM per assistenza.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per personalizzare il Azure-SSIS IR sono necessari gli elementi seguenti:

- [Una sottoscrizione di Azure](https://azure.microsoft.com/)

- [Eseguire il provisioning del runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un account di archiviazione di Azure](https://azure.microsoft.com/services/storage/). Non richiesto per le configurazioni personalizzate rapide. Per le configurazioni personalizzate standard, è possibile caricare e archiviare lo script di installazione personalizzato e i file associati in un contenitore BLOB. Il processo di installazione personalizzata carica nello stesso contenitore BLOB anche il log di esecuzione.

## <a name="instructions"></a>Istruzioni

1. Se si vuole configurare o riconfigurare il Azure-SSIS IR con PowerShell, scaricare e installare [Azure PowerShell](/powershell/azure/install-az-ps). Per le configurazioni personalizzate rapide, andare al passaggio 4.

1. Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

   * È necessario disporre di un file di script denominato *Main. cmd*, che rappresenta il punto di ingresso dell'installazione personalizzata.  
   * Per assicurarsi che lo script possa essere eseguito in modalità invisibile all'utente, è consigliabile testarlo prima sul computer locale.  
   * Se si desidera che i log aggiuntivi generati da altri strumenti, ad esempio *msiexec. exe*, vengano caricati nel contenitore, specificare la variabile di ambiente predefinita, `CUSTOM_SETUP_SCRIPT_LOG_DIR`come cartella dei log negli script (ad esempio, *msiexec/i xxx. msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Scaricare, installare e aprire [Azure Storage Explorer](https://storageexplorer.com/). A tale scopo, procedere come indicato di seguito:

   a. In **(locale e collegato)** fare clic con il pulsante destro del mouse su **account di archiviazione**e quindi scegliere **Connetti ad archiviazione di Azure**.

      ![Connetti ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selezionare **Usa nome e chiave dell'account di archiviazione**e quindi fare clic su **Avanti**.

      ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Immettere il nome e la chiave dell'account di archiviazione di Azure, fare clic su **Avanti**e quindi selezionare **Connetti**.

      ![Specificare il nome e la chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Nell'account di archiviazione di Azure connesso, fare clic con il pulsante destro del mouse su **contenitori BLOB**, scegliere **Crea contenitore BLOB**e assegnare un nome al nuovo contenitore.

      ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare *Main. cmd* nel primo livello del contenitore, non in una cartella. Assicurarsi inoltre che il contenitore includa solo i file di installazione personalizzati necessari, in modo che il download in Azure-SSIS IR in un secondo momento non richiederà molto tempo. La durata massima di un'installazione personalizzata è attualmente impostata su 45 minuti prima del timeout. È incluso il tempo necessario per scaricare tutti i file dal contenitore e installarli nel Azure-SSIS IR. Se il programma di installazione richiede più tempo, generare un ticket di supporto.

      ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Fare clic con il pulsante destro del mouse sul contenitore, quindi scegliere **Ottieni firma di accesso condiviso**.

      ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Creare l'URI di firma di accesso condiviso per il contenitore con una data di scadenza sufficientemente lungo e con autorizzazione di lettura/scrittura/elenco. È necessario l'URI di firma di accesso condiviso per scaricare ed eseguire lo script di installazione personalizzato e i file associati ogni volta che viene ricreata l'immagine o il riavvio di un nodo del Azure-SSIS IR. Le autorizzazioni di scrittura sono necessarie per caricare i log di esecuzione dell'installazione.

      > [!IMPORTANT]
      > Verificare che l'URI di firma di accesso condiviso non scada e che le risorse di installazione personalizzate siano sempre disponibili durante l'intero ciclo di vita del Azure-SSIS IR, dalla creazione all'eliminazione, soprattutto se si arresta e si avvia regolarmente il Azure-SSIS IR durante questo periodo.

      ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copiare e salvare l'URI SAS del contenitore.

      ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando si configura o si riconfigura la Azure-SSIS IR con un'interfaccia utente di data factory, è possibile aggiungere o rimuovere le configurazioni personalizzate selezionando la casella di controllo **Personalizza il Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema** o le installazioni dei componenti nella sezione **Impostazioni avanzate** del riquadro di **installazione di Integration Runtime** . 

   Se si desidera aggiungere configurazioni personalizzate standard, immettere l'URI SAS del contenitore nella casella URI di firma di accesso condiviso del **contenitore personalizzato** . 
   
   Se si desidera aggiungere impostazioni personalizzate rapide, selezionare **nuovo** per aprire il riquadro **Aggiungi installazione personalizzata rapida** , quindi selezionare un tipo nell'elenco a discesa **tipo di installazione personalizzata Express** :

   * Se si seleziona il tipo di **comando Esegui cmdkey** , è possibile rendere permanente le credenziali di accesso per le condivisioni file o file di Azure condivisioni Azure-SSIS IR immettendo il nome del computer o il nome di dominio, il nome account o il nome utente e la chiave dell'account o la password nelle caselle **/Add**, **/User**e **/pass** . Questa operazione è simile all'esecuzione del comando Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) nel computer locale.
   
   * Se si seleziona il tipo **Aggiungi variabile di ambiente** , è possibile aggiungere le variabili di ambiente Windows da usare nei pacchetti in esecuzione nel Azure-SSIS IR immettendo il nome e il valore della variabile di ambiente nelle caselle **nome variabile** e **valore variabile** . Questa operazione è simile all'esecuzione del comando Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) nel computer locale.

   * Se si seleziona il tipo di **componente installa con licenza** , è possibile selezionare un componente integrato dai partner ISV nell'elenco a discesa **nome componente** :

     * Se si seleziona il componente della **Factory delle attività di SentryOne** , è possibile installare la suite di factory delle [attività](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) dei componenti da SentryOne nel Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato dall'utente nella casella **chiave di licenza** . La versione integrata corrente è **2019.4.3**.

     * Se si seleziona il **Hedda oh22's. Componente IO** , è possibile installare [Hedda. ](https://hedda.io/ssis-component/)Componente per la qualità dei dati e la pulizia dei dati di i/o da oh22 nel Azure-SSIS IR dopo l'acquisto del servizio. La versione integrata corrente è **1.0.13**.

     * Se si seleziona il componente **oh22's SQLPhonetics.NET** , è possibile installare il componente [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data Quality/matching da oh22 nella Azure-SSIS IR immettendo il codice Product Key acquistato da essi nella casella **chiave di licenza** . La versione integrata corrente è **1.0.43**.

     * Se si seleziona il componente **SSIS Integration Toolkit di KingswaySoft** , è possibile installare la suite di connettori [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) per le app CRM/ERP/marketing/collaborazione, ad esempio Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud e così via, da KingswaySoft nel Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato da essi nella casella **chiave di licenza** . La versione integrata corrente è **2019,2**.

     * Se si seleziona il componente **SSIS Productivity Pack di KingswaySoft** , è possibile installare i componenti di [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) suite di componenti da KingswaySoft nel Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato da essi nella casella **chiave di licenza** . La versione integrata corrente è **10,0**.

   Le impostazioni di aggiunta personalizzate Express verranno visualizzate nella sezione **Impostazioni avanzate** . Per rimuoverli, selezionare le caselle di controllo e quindi selezionare **Elimina**.

   ![Impostazioni avanzate con configurazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando si configura o si riconfigura la Azure-SSIS IR con PowerShell, è possibile aggiungere o rimuovere le configurazioni personalizzate eseguendo il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` prima di avviare il Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   Al termine dell'installazione personalizzata standard e il Azure-SSIS IR viene avviato, è possibile trovare l'output standard di *Main. cmd* e altri log di esecuzione nella cartella *Main. cmd. log* del contenitore di archiviazione.

1. Per visualizzare alcuni esempi di configurazioni standard personalizzate, connettersi al contenitore di anteprima pubblica usando Azure Storage Explorer.

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

      ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selezionare **Usa un URI** di firma di accesso condiviso e quindi nella casella **URI** immettere il seguente URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selezionare **Avanti**, quindi selezionare **Connetti**.

   d. Nel riquadro sinistro selezionare il contenitore Connected **publicpreview** , quindi fare doppio clic sulla cartella *CustomSetupScript* . Questa cartella contiene gli elementi seguenti:

      * Una cartella di *esempio* , che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo della Azure-SSIS IR. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene inoltre una cartella *gacutil* , il cui intero contenuto (*gacutil. exe*, *gacutil. exe. config*e *1033 \ gacutlrc. dll*) può essere copiato nel contenitore.

      * Una cartella *UserScenarios* , che contiene diversi esempi di installazione personalizzata da scenari utente reali.

        ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Fare doppio clic sulla cartella *UserScenarios* per trovare gli elementi seguenti:

      * Una cartella di *.NET FRAMEWORK 3,5* , che contiene un'installazione personalizzata per installare una versione precedente del .NET Framework che potrebbe essere necessaria per i componenti personalizzati in ogni nodo della Azure-SSIS IR.

      * Una cartella *bcp* , che contiene un'installazione personalizzata per installare SQL Server utilità della riga di comando (*MsSqlCmdLnUtils. msi*), incluso il programma per la copia bulk (*bcp*), in ogni nodo della Azure-SSIS IR.

      * Una cartella di *Excel* , che contiene uno script di installazione personalizzato (*Main. cmd*) C# per installare assembly e librerie che è possibile usare nelle attività script per leggere e scrivere in modo dinamico i file di Excel in ogni nodo della Azure-SSIS IR. 
      
        Prima di tutto, scaricare [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat. OPENXML. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), quindi caricarli tutti insieme a *Main. cmd* nel contenitore. In alternativa, se si desidera utilizzare solo la gestione connessione Excel standard, l'origine Excel e la destinazione Excel, il ridistribuibile di accesso richiesto è già preinstallato nel Azure-SSIS IR, pertanto non è necessaria alcuna installazione personalizzata.
      
      * Una cartella *MySQL ODBC* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare i driver ODBC MySQL in ogni nodo della Azure-SSIS IR. Questa installazione consente di utilizzare la gestione connessione ODBC, l'origine e la destinazione per la connessione al server MySQL. 
     
        Scaricare prima di tutto [le versioni più recenti a 64 bit e a 32 bit dei programmi di installazione dei driver ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (ad esempio, *MySQL-Connector-ODBC-8.0.13-Winx64. msi* e *MySQL-Connector-ODBC-8.0.13-Win32. msi*), quindi caricarli tutti insieme a *Main. cmd* nel contenitore.

      * Una cartella *Oracle Enterprise* , che contiene uno script di installazione personalizzato (*Main. cmd*) e un file di configurazione dell'installazione invisibile all'utente (*client. rsp*) per installare i connettori Oracle e il driver OCI in ogni nodo della Azure-SSIS IR Enterprise Edition. Questa installazione consente di utilizzare la gestione connessione, l'origine e la destinazione Oracle per la connessione al server Oracle. 
      
        Scaricare prima di tutto i connettori Microsoft v 5.0 per Oracle (*AttunitySSISOraAdaptersSetup. msi* e *AttunitySSISOraAdaptersSetup64. msi*) dall' [area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e il client oracle più recente, ad esempio *winx64_12102_client. zip*, da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)e quindi caricarli tutti insieme a *Main. cmd* e *client. rsp* nel contenitore. Se si usa TNS per connettersi a Oracle, è anche necessario scaricare *tnsnames. ora*, modificarlo e caricarlo nel contenitore, in modo che possa essere copiato nella cartella di installazione di Oracle durante l'installazione.

      * Una cartella *ADO.NET di Oracle standard* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare il driver Oracle ODP.NET in ogni nodo della Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ADO.NET per la connessione al server Oracle. 
      
        Prima di tutto, [scaricare il driver Oracle ODP.NET più recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (ad esempio, *ODP. NET_Managed_ODAC122cR1. zip*) e quindi caricarlo insieme a *Main. cmd* nel contenitore.
       
      * Una cartella *ODBC standard di Oracle* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare il driver ODBC Oracle e configurare il nome dell'origine dati (DSN) in ogni nodo del Azure-SSIS IR. Questa installazione consente di utilizzare la gestione connessione ODBC, l'origine e la destinazione o Power Query gestione connessione e l'origine con il tipo di origine dati ODBC per la connessione al server Oracle. 
      
        Prima di tutto, scaricare la versione più recente di Oracle Instant client (pacchetto Basic o pacchetto Lite di base) e il pacchetto ODBC e quindi caricarli tutti insieme a *Main. cmd* nel contenitore:
        * [Scaricare pacchetti a 64 bit](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacchetto di base: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Pacchetto Lite di base: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; Pacchetto ODBC: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [Scaricare pacchetti a 32 bit](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacchetto di base: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Pacchetto Lite di base: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; Pacchetto ODBC: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * Una cartella *Oracle standard OLEDB* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare il driver Oracle OLEDB in ogni nodo della Azure-SSIS IR. Questa installazione consente di utilizzare la gestione connessione, l'origine e la destinazione OLEDB per la connessione al server Oracle. 
     
        Scaricare prima di tutto [il driver Oracle OLEDB più recente](https://www.oracle.com/partners/campaign/index-090165.html) (ad esempio, *ODAC122010Xcopy_x64. zip*) e quindi caricarlo insieme a *Main. cmd* nel contenitore.

      * Una cartella *ODBC PostgreSQL* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare i driver ODBC PostgreSQL in ogni nodo della Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ODBC per connettersi al server PostgreSQL. 
     
        Scaricare prima di tutto [le versioni più recenti a 64 bit e a 32 bit dei programmi di installazione del driver ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) , ad esempio *psqlodbc_x64. msi* e *psqlodbc_x86. msi*, quindi caricarli tutti insieme a *Main. cmd* nel contenitore.

      * Una cartella *SAP BW* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare l'assembly di SAP .NET Connector (*librfc32. dll*) in ogni nodo della Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione SAP Business Warehouse (BW) per connettersi al server SAP BW. 
      
        Per prima cosa, caricare la versione a 64 bit o a 32 bit di *librfc32. dll* dalla cartella di installazione di SAP insieme a *Main. cmd* nel contenitore. Lo script copia quindi l'assembly SAP nella cartella *%windir%\SysWOW64.* o *%windir%\System32* durante l'installazione.

      * Una cartella di *archiviazione* , che contiene un'installazione personalizzata per installare Azure PowerShell in ogni nodo della Azure-SSIS IR. Questa installazione consente di distribuire ed eseguire pacchetti SSIS che eseguono [script di PowerShell per manipolare l'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copiare *Main. cmd*, un esempio *AzurePowerShell. msi* (oppure usare la versione più recente) e *storage. ps1* nel contenitore. Usare *PowerShell. dtsx* come modello per i pacchetti. Il modello di pacchetto combina un' [attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), che Scarica *storage. ps1* come uno script di PowerShell modificabile e un' [attività Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), che esegue lo script in ogni nodo.

      * Una cartella *Teradata* , che contiene uno script di installazione personalizzato (*Main. cmd*), il file associato (*Install. cmd*) e i pacchetti del programma di installazione (*MSI*). Questi file installano i connettori Teradata, l'API Teradata Parallel Transporter (TPT) e il driver ODBC in ogni nodo della Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Teradata per la connessione al server Teradata. 
      
        Per prima cosa, [scaricare il file zip strumenti e utilità Teradata 15. x](http://partnerintelligence.teradata.com) , ad esempio *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*, quindi caricarlo insieme ai file con *estensione cmd* e *MSI* indicati in precedenza nel contenitore.

      * Una cartella *Zulu OPENJDK* , che contiene uno script di installazione personalizzato (*Main. cmd*) e un file di PowerShell (*install_openjdk. ps1*) per installare il OPENJDK Zulu in ogni nodo della Azure-SSIS IR. Questa configurazione consente di usare Azure Data Lake Store e connettori di file flessibili per elaborare i file ORC e parquet. Per ulteriori informazioni, vedere il [Feature Pack di Azure per Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Prima di tutto, [scaricare la versione più recente di Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (ad esempio, *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*) e quindi caricarla insieme a *Main. cmd* e *install_openjdk. ps1* nel contenitore.

        ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Per provare questi esempi di installazione personalizzata, copiare il contenuto dalla cartella selezionata nel contenitore.
   
      Quando si configura o si riconfigura il Azure-SSIS IR usando l'interfaccia utente di Data Factory, selezionare la casella di controllo **Personalizza la Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema/le installazioni dei componenti** nella sezione **Impostazioni avanzate** e quindi immettere l'URI SAS del contenitore nella casella URI di firma di accesso condiviso del **contenitore personalizzato** .
   
      Quando si configura o si riconfigura la Azure-SSIS IR con PowerShell, eseguire il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` con l'URI SAS del contenitore come valore per `SetupScriptContainerSasUri` parametro.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'edizione Enterprise di Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Sviluppare componenti personalizzati a pagamento o con licenza per il Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
