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
ms.date: 09/15/2020
ms.openlocfilehash: ff56ef55066fd0f56b5b8dc7507bfb63b8b8baa0
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090602"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizzare l'installazione di Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

È possibile personalizzare il Integration Runtime (IR) Azure-SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) tramite configurazioni personalizzate. Consentono di aggiungere passaggi personalizzati durante il provisioning o la riconfigurazione del Azure-SSIS IR. 

Utilizzando le configurazioni personalizzate, è possibile modificare la configurazione operativa o l'ambiente predefinito del Azure-SSIS IR. Ad esempio, per avviare altri servizi di Windows, rendere permanente le credenziali di accesso per le condivisioni file oppure usare un protocollo di crittografia avanzata/più sicuro (TLS 1,2). In alternativa, è possibile installare componenti aggiuntivi, ad esempio assembly, driver o estensioni, in ogni nodo di Azure-SSIS IR. Possono essere componenti personalizzati, open source o di terze parti. Per altre informazioni sui componenti predefiniti/preinstallati, vedere [Componenti predefiniti e preinstallati in Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).

È possibile eseguire installazioni personalizzate in Azure-SSIS IR in uno dei due modi seguenti: 
* **Installazione personalizzata standard con uno script**: preparare uno script e i relativi file associati e caricarli in un contenitore BLOB nell'account di archiviazione di Azure. Si specifica quindi l'URI (Uniform Resource Identifier) di una firma di accesso condiviso per il contenitore durante l'installazione o la riconfigurazione di Azure-SSIS IR. Ogni nodo di Azure-SSIS IR scarica quindi lo script e i file associati dal contenitore ed esegue l'installazione personalizzata con autorizzazioni elevate. Al termine dell'installazione personalizzata, ogni nodo carica l'output standard dell'esecuzione e gli altri log nel contenitore.
* **Impostazione rapida personalizzata senza uno script**: eseguire alcune configurazioni di sistema comuni e i comandi di Windows oppure installare alcuni componenti aggiuntivi popolari o consigliati senza usare alcuno script.

È possibile installare sia i componenti gratuiti (senza licenza) che quelli a pagamento (con licenza) con configurazioni standard ed Express personalizzate. Se si è un fornitore di software indipendente (ISV), vedere [sviluppare componenti a pagamento o concessi in licenza per Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Per avere a disposizione i miglioramenti futuri, è consigliabile usare le serie di nodi v3 o successive per Azure-SSIS IR con l'installazione personalizzata.

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti si applicano solo alle installazioni personalizzate standard:

- Se si vuole usare *gacutil.exe* nello script per installare assembly in Global Assembly Cache (GAC), è necessario specificare *gacutil.exe* come parte dell'installazione personalizzata. In alternativa, è possibile usare la copia fornita nella cartella *Sample* del contenitore di *anteprima pubblica* , vedere la sezione **esempi di installazione personalizzata standard** riportata di seguito.

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

È possibile eseguire il provisioning o riconfigurare la Azure-SSIS IR con configurazioni personalizzate nell'interfaccia utente di ADF. Se si vuole eseguire la stessa operazione usando PowerShell, scaricare e installare [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Installazione personalizzata standard

Per eseguire il provisioning o riconfigurare la Azure-SSIS IR con le configurazioni personalizzate standard nell'interfaccia utente di ADF, seguire questa procedura.

1. Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

   * È necessario avere un file di script denominato *main.cmd*, che è il punto di ingresso dell'installazione personalizzata.  
   * Per assicurarsi che lo script possa essere eseguito automaticamente, è necessario testarlo prima nel computer locale.  
   * Se si vuole caricare nel contenitore i log aggiuntivi generati da altri strumenti (ad esempio *msiexec.exe*), specificare negli script la variabile di ambiente predefinita `CUSTOM_SETUP_SCRIPT_LOG_DIR` come cartella dei log (ad esempio *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Scaricare, installare e aprire [Azure Storage Explorer](https://storageexplorer.com/).

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Storage Accounts** (Account di archiviazione) e quindi selezionare **Connect to Azure storage** (Connetti ad archiviazione di Azure).

      ![Connettersi ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e quindi selezionare **Next** (Avanti).

      ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Immettere il nome dell'account di archiviazione di Azure e la chiave, selezionare **Next** (Avanti), quindi selezionare **Connect** (Connetti).

      ![Specificare il nome e la chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Nell'account di archiviazione di Azure connesso fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB), selezionare **Create Blob Container** (Crea contenitore BLOB) e assegnare un nome al nuovo contenitore.

      ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare *main.cmd* nel primo livello del contenitore, non in una cartella qualsiasi. Il contenitore deve contenere solo i file di installazione personalizzati necessari, quindi il download in Azure-SSIS IR in un secondo momento non richiederà molto tempo. La durata massima di un'installazione personalizzata è attualmente impostata su 45 minuti prima del timeout. È incluso il tempo necessario per scaricare tutti i file dal contenitore e installarli in Azure-SSIS IR. Se l'installazione richiede più tempo, generare un ticket di supporto.

      ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Fare clic con il pulsante destro del mouse sul contenitore e quindi selezionare **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

      ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Creare l'URI SAS per il contenitore con una scadenza sufficientemente lunga e con autorizzazioni di lettura, scrittura ed elenco. È necessario l'URI SAS per scaricare ed eseguire lo script di installazione personalizzato e i file associati. Questa situazione si verifica ogni volta che viene ricreata l'immagine o il riavvio di un nodo del Azure-SSIS IR. È inoltre necessaria l'autorizzazione di scrittura per caricare i log di esecuzione dell'installazione.

      > [!IMPORTANT]
      > Assicurarsi che l'URI SAS non scada e che le risorse del programma di installazione siano sempre disponibili durante l'intero ciclo di vita del runtime di integrazione Azure-SSIS, dalla creazione all'eliminazione, soprattutto se si arresta e si riavvia regolarmente il runtime di integrazione Azure-SSIS durante questo periodo.

      ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copiare e salvare l'URI SAS del contenitore.

      ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Selezionare la casella di controllo **Personalizza le Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema/installazioni dei componenti** nella pagina **Impostazioni avanzate** del riquadro di **installazione di Integration Runtime** . Quindi, immettere l'URI SAS del contenitore nella casella di testo URI di firma di accesso condiviso del **contenitore personalizzato** .

   ![Impostazioni avanzate per le installazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Al termine dell'installazione personalizzata standard e la Azure-SSIS IR viene avviata, è possibile trovare tutti i log di installazione personalizzati nella cartella *Main. cmd. log* del contenitore. Includono l'output standard di *Main. cmd* e altri log di esecuzione.

### <a name="express-custom-setup"></a>Installazione personalizzata rapida

Per eseguire il provisioning o riconfigurare la Azure-SSIS IR con le configurazioni personalizzate rapide nell'interfaccia utente di ADF, seguire questa procedura.

1. Selezionare la casella di controllo **Personalizza le Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema/installazioni dei componenti** nella pagina **Impostazioni avanzate** del riquadro di **installazione di Integration Runtime** . 

1. Selezionare **nuovo** per aprire il riquadro **Aggiungi installazione personalizzata rapida** , quindi selezionare un tipo nell'elenco a discesa **tipo di installazione personalizzata Express** . Sono attualmente disponibili configurazioni personalizzate Express per l'esecuzione del comando cmdkey, l'aggiunta di variabili di ambiente, l'installazione di Azure PowerShell e l'installazione di componenti con licenza.

#### <a name="running-cmdkey-command"></a>Esecuzione del comando cmdkey

Se si seleziona il tipo di **comando Run cmdkey** per l'installazione personalizzata rapida, è possibile eseguire il comando Windows cmdkey nel Azure-SSIS IR. A tale scopo, immettere rispettivamente il nome del computer o il nome di dominio, il nome utente o il nome dell'account e la password o la chiave dell'account specificati nelle caselle di testo **/Add**, **/User**e **/pass** . In questo modo sarà possibile rendere permanente le credenziali di accesso per server SQL, condivisioni file o File di Azure nel Azure-SSIS IR. Per accedere File di Azure, ad esempio, è possibile immettere `YourAzureStorageAccountName.file.core.windows.net` `azure\YourAzureStorageAccountName` rispettivamente, e `YourAzureStorageAccountKey` per **/Add**, **/User**e **/pass**. Questa operazione è simile all'esecuzione del comando di Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) nel computer locale.

#### <a name="adding-environment-variables"></a>Aggiunta di variabili di ambiente

Se si seleziona il tipo **Aggiungi variabile di ambiente** per l'installazione personalizzata rapida, è possibile aggiungere una variabile di ambiente Windows nel Azure-SSIS IR. A tale scopo, immettere il nome e il valore della variabile di ambiente nelle caselle di testo **nome** variabile e **valore variabile** , rispettivamente. Ciò consentirà di usare la variabile di ambiente nei pacchetti eseguiti in Azure-SSIS IR, ad esempio in componenti/attività script. Questa operazione è simile all'esecuzione del comando di Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) nel computer locale.

#### <a name="installing-azure-powershell"></a>Installazione di Azure PowerShell

Se si seleziona il tipo di **installazione di Azure PowerShell** per l'installazione personalizzata rapida, è possibile installare il modulo AZ di PowerShell nel Azure-SSIS IR. A tale scopo, immettere il numero di versione del modulo AZ (x. y. z) desiderato da un [elenco di quelli supportati](https://www.powershellgallery.com/stats/packages/Az?groupby=Version). Questo consentirà di eseguire Azure PowerShell cmdlet/script nei pacchetti per gestire le risorse di Azure, ad esempio [Azure Analysis Services (AAS)](https://docs.microsoft.com/azure/analysis-services/analysis-services-powershell).

#### <a name="installing-licensed-components"></a>Installazione dei componenti con licenza

Se si seleziona il tipo di **componente installa con licenza** per l'installazione personalizzata rapida, è possibile selezionare un componente integrato dai partner ISV nell'elenco a discesa **nome componente** :

   * Se si seleziona il componente **Factory delle attività di SentryOne** , è possibile installare la suite di factory delle [attività](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) dei componenti da SentryOne nel Azure-SSIS IR. A tale scopo, immettere prima di tutto la chiave di licenza del prodotto acquistata in precedenza nella casella di testo **chiave di licenza** . La versione integrata corrente è **2020.1.3**.

   * Se si seleziona il **Hedda oh22's. Componente IO** , è possibile installare [Hedda. ](https://hedda.io/ssis-component/) Componente per la pulizia e la qualità dei dati di i/o da oh22 nel Azure-SSIS IR. A tale scopo, è necessario acquistare il servizio in anticipo. La versione integrata corrente è **1.0.14**.

   * Se si seleziona il componente **oh22's SQLPhonetics.NET** , è possibile installare il componente [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/matching da oh22 nel Azure-SSIS IR. A tale scopo, immettere prima di tutto la chiave di licenza del prodotto acquistata in precedenza nella casella di testo **chiave di licenza** . La versione integrata corrente è **1.0.45**.

   * Se si seleziona il componente **SSIS Integration Toolkit di KingswaySoft** , è possibile installare la suite di connettori [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) per le app CRM/ERP/marketing/collaborazione, ad esempio Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud e così via, da KingswaySoft nel Azure-SSIS IR. A tale scopo, immettere prima di tutto la chiave di licenza del prodotto acquistata in precedenza nella casella di testo **chiave di licenza** . La versione integrata corrente è **2020,1**.

   * Se si seleziona il componente **SSIS Productivity Pack di KingswaySoft** , è possibile installare la suite [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) dei componenti da KingswaySoft nel Azure-SSIS IR. A tale scopo, immettere prima di tutto la chiave di licenza del prodotto acquistata in precedenza nella casella di testo **chiave di licenza** . La versione integrata corrente è **20,1**.

   * Se si seleziona il componente **Estrai del software di Theobald** , è possibile installare [Estrai è](https://theobald-software.com/en/xtract-is/) una suite di connettori per i sistemi SAP (ERP, s/4HANA, BW) del software Theobald sul Azure-SSIS IR. A tale scopo, trascinare & eliminare/caricare il file di licenza del prodotto acquistato in precedenza nella casella di input del **file di licenza** . La versione integrata corrente è **6.1.1.3**.

   * Se si seleziona il componente del **servizio di integrazione di AecorSoft** , è possibile installare la suite [Integration Services](https://www.aecorsoft.com/en/products/integrationservice) dei connettori per i sistemi SAP e Salesforce da AecorSoft nel Azure-SSIS IR. A tale scopo, immettere prima di tutto la chiave di licenza del prodotto acquistata in precedenza nella casella di testo **chiave di licenza** . La versione integrata corrente è **3.0.00**.

Le impostazioni di aggiunta personalizzate Express verranno visualizzate nella pagina **Impostazioni avanzate** . Per rimuoverli, selezionare le caselle di controllo corrispondenti e quindi selezionare **Elimina**.

### <a name="azure-powershell"></a>Azure PowerShell

Per eseguire il provisioning o riconfigurare la Azure-SSIS IR con le configurazioni personalizzate con Azure PowerShell, seguire questa procedura.

1. Se il Azure-SSIS IR è già avviato o in esecuzione, arrestarlo per primo.

1. È quindi possibile aggiungere o rimuovere le configurazioni personalizzate eseguendo il `Set-AzDataFactoryV2IntegrationRuntime` cmdlet prima di avviare il Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
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
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
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

### <a name="standard-custom-setup-samples"></a>Esempi di installazione personalizzata standard

Per visualizzare e riutilizzare alcuni esempi di configurazioni personalizzate standard, completare i passaggi seguenti.

1. Connettersi al contenitore di anteprima pubblica usando Azure Storage Explorer.

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

      ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selezionare **Usa un URI** di firma di accesso condiviso e quindi nella casella di testo **URI** immettere l'URI SAS seguente:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selezionare **Next** (Avanti) e quindi **Connect** (Connetti).

   d. Nel riquadro sinistro selezionare il contenitore **publicpreview** connesso, quindi fare doppio clic sulla cartella *CustomSetupScript*. Questa cartella contiene gli elementi seguenti:

      * Una cartella *Sample* che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo di Azure-SSIS IR. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene anche una cartella *gacutil* di cui è possibile copiare l'intero contenuto (*gacutil.exe*, *gacutil.exe.config* e *1033\gacutlrc.dll*) nel contenitore.

      * Una cartella *UserScenarios* che contiene diversi esempi di installazioni personalizzate di scenari utente reali.

        ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Fare doppio clic sulla cartella *UserScenarios* per trovare gli elementi seguenti:

      * Una cartella di *.NET FRAMEWORK 3,5* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare una versione precedente del .NET Framework in ogni nodo della Azure-SSIS IR. Questa versione potrebbe essere richiesta da alcuni componenti personalizzati.

      * Una cartella *bcp* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare SQL Server le utilità della riga di comando (*MsSqlCmdLnUtils.msi*) in ogni nodo della Azure-SSIS IR. Una di queste utilità è il programma per la copia bulk (*bcp*).

      * Una cartella di *Excel* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare alcuni assembly e librerie C# in ogni nodo della Azure-SSIS IR. È possibile usarli nelle attività script per leggere e scrivere in modo dinamico i file di Excel. 
      
        Innanzitutto, scaricare [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) e quindi caricarli insieme a *main.cmd* nel contenitore. In alternativa, se si desidera utilizzare solo i connettori standard di Excel (gestione connessione, origine e destinazione), il ridistribuibile di Access che li contiene è già preinstallato nel Azure-SSIS IR, quindi non è necessaria alcuna installazione personalizzata.
      
      * Una cartella *MYSQL ODBC* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC di MySQL in ogni nodo di Azure-SSIS IR. Questa installazione consente di utilizzare i connettori ODBC (gestione connessione, origine e destinazione) per connettersi al server MySQL. 
     
        Innanzitutto, [scaricare le versioni a 64 bit e a 32 bit più recenti dei programmi di installazione dei driver ODBC di MySQL](https://dev.mysql.com/downloads/connector/odbc/) (ad esempio *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*) e quindi caricarle insieme a *main.cmd* nel contenitore.

      * Una cartella *ORACLE ENTERPRISE* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di configurazione per l'installazione invisibile all'utente (*client.rsp*) dei connettori e del driver OCI di Oracle in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di utilizzare la gestione connessione, l'origine e la destinazione Oracle per la connessione al server Oracle. 
      
        Scaricare prima di tutto i connettori Microsoft v 5.0 per Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) dall' [area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e il client oracle più recente, ad esempio *winx64_12102_client.zip*, da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html). Successivamente, caricare tutti insieme con *Main. cmd* e *client. rsp* nel contenitore. Se si usa TNS per connettersi a Oracle, è anche necessario scaricare *tnsnames. ora*, modificarlo e caricarlo nel contenitore. In questo modo, può essere copiato nella cartella di installazione di Oracle durante l'installazione.

      * Una cartella *ORACLE STANDARD ADO.NET* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver ODP.NET di Oracle in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ADO.NET per la connessione al server Oracle. 
      
        Innanzitutto, [scaricare il driver ODP.NET di Oracle più recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (ad esempio *ODP.NET_Managed_ODAC122cR1.zip*) e quindi caricarlo insieme a *main.cmd* nel contenitore.
       
      * Una cartella *ODBC standard di Oracle* , che contiene uno script di installazione personalizzato (*Main. cmd*) per installare il driver ODBC Oracle in ogni nodo della Azure-SSIS IR. Lo script configura anche il nome dell'origine dati (DSN). Questa installazione consente di utilizzare la gestione connessione ODBC, l'origine e la destinazione o Power Query gestione connessione e l'origine con il tipo di origine dati ODBC per la connessione al server Oracle. 
      
        Innanzitutto, scaricare la versione più recente di Oracle Instant Client (Basic Package o Basic Lite Package) e il pacchetto ODBC e quindi caricarli insieme a *main.cmd* nel contenitore:
        * [Scaricare i pacchetti a 64 bit](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic Package: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite Package: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Scaricare i pacchetti a 32 bit](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite Package: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Una cartella *ORACLE STANDARD OLEDB* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver OLEDB di Oracle in ogni nodo di Azure-SSIS IR. Questa installazione consente di utilizzare la gestione connessione, l'origine e la destinazione OLEDB per la connessione al server Oracle. 
     
        Innanzitutto, [scaricare il driver OLEDB di Oracle più recente](https://www.oracle.com/partners/campaign/index-090165.html) (ad esempio *ODAC122010Xcopy_x64.zip*) e quindi caricarlo insieme a *main.cmd* nel contenitore.

      * Una cartella *POSTGRESQL ODBC* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC di PostgreSQL in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ODBC per connettersi al server PostgreSQL. 
     
        Innanzitutto, [scaricare le versioni a 64 bit e a 32 bit più recenti dei programmi di installazione dei driver ODBC di PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (ad esempio *psqlodbc_x64.msi* e *psqlodbc_x86.msi*) e quindi caricarle insieme a *main.cmd* nel contenitore.

      * Una cartella *SAP BW* che contiene uno script di installazione personalizzato (*main.cmd*) per installare l'assembly SAP .NET Connector (*librfc32.dll*) in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di utilizzare la gestione connessione, l'origine e la destinazione SAP BW per la connessione al server di SAP BW. 
      
        Innanzitutto, caricare la versione a 64 bit o a 32 bit di *librfc32.dll* dalla cartella di installazione SAP insieme a *main.cmd* nel contenitore. Lo script copia quindi l'assembly SAP nella cartella *%windir%\SysWow64* o *%windir%\System32* durante l'installazione.

      * Una cartella *STORAGE* che contiene uno script di installazione personalizzato (*main.cmd*) per installare Azure PowerShell in ogni nodo di Azure-SSIS IR. Questa installazione consente di distribuire ed eseguire pacchetti SSIS che eseguono [Azure PowerShell cmdlet/script per gestire l'archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copiare *main.cmd*, *AzurePowerShell. msi* di esempio (oppure usare la versione più recente) e *storage.ps1* nel contenitore. Usare *PowerShell.dtsx* come modello per i pacchetti. Il modello di pacchetto combina un' [attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), che Scarica uno script di PowerShell modificabile (*storage.ps1*) e un' [attività Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), che esegue lo script in ogni nodo.

      * Una cartella *TERADATA* che contiene uno script di installazione personalizzato (*main.cmd*), il file associato (*install.cmd*) e i pacchetti di installazione ( *.msi*). Questi file installano i connettori Teradata, l'API TPT (Teradata Parallel Transporter) e il driver ODBC in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Teradata per la connessione al server Teradata. 
      
        Innanzitutto, [scaricare il file ZIP di Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (ad esempio *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) e quindi caricarlo insieme ai file *.cmd* e *.msi* indicati in precedenza nel contenitore.

      * Una cartella *tls 1,2* , che contiene uno script di installazione personalizzato (*Main. cmd*) per usare la crittografia avanzata e il protocollo di rete più sicuro (TLS 1,2) in ogni nodo della Azure-SSIS IR. Lo script disabilita anche le versioni precedenti di SSL/TLS.

      * Una cartella *ZULU OPENJDK* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di PowerShell (*install_openjdk.ps1*) per installare Zulu OpenJDK in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare Azure Data Lake Store e connettori File flessibili per elaborare i file ORC e Parquet. Per altre informazioni, vedere [Feature Pack di Azure per Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Innanzitutto, [scaricare la versione più recente di Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (ad esempio *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) e quindi caricarla insieme a *main.cmd* e *install_openjdk.ps1* nel contenitore.

        ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Per riutilizzare questi esempi di installazione personalizzata standard, copiare il contenuto della cartella selezionata nel contenitore.

1. Quando si esegue il provisioning o si riconfigurano i Azure-SSIS IR nell'interfaccia utente di ADF, selezionare la casella di controllo **Personalizza l'Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema/Installazione componenti** nella pagina **Impostazioni avanzate** del riquadro di **installazione di Integration Runtime** . Quindi, immettere l'URI SAS del contenitore nella casella di testo URI di firma di accesso condiviso del **contenitore personalizzato** .
   
1. Quando si esegue il provisioning o si riconfigura il Azure-SSIS IR usando Azure PowerShell, arrestarlo se è già avviato o in esecuzione, eseguire il `Set-AzDataFactoryV2IntegrationRuntime` cmdlet con l'URI di firma di accesso condiviso del contenitore come valore per il `SetupScriptContainerSasUri` parametro e quindi avviare il Azure-SSIS IR.

1. Al termine dell'installazione personalizzata standard e la Azure-SSIS IR viene avviata, è possibile trovare tutti i log di installazione personalizzati nella cartella *Main. cmd. log* del contenitore. Includono l'output standard di *Main. cmd* e altri log di esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'edizione Enterprise di Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Sviluppare componenti a pagamento o concessi in licenza per Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
