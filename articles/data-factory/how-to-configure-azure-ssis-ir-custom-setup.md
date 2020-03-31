---
title: Personalizzare la configurazione per un runtime di integrazione Azure-SSISCustomize the setup for an Azure-SSIS Integration Runtime
description: Questo articolo descrive come usare l'interfaccia di installazione personalizzata per un runtime di integrazione Azure-SSIS per installare componenti aggiuntivi o modificare le impostazioni
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
ms.date: 03/27/2020
ms.openlocfilehash: d6252b7a0ecce553bc3a1519055375fd4cd034f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336196"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizzare la configurazione per un runtime di integrazione Azure-SSISCustomize the setup for an Azure-SSIS Integration Runtime

L'installazione personalizzata per un runtime di integrazione di Azure-SQL Server Integration Services (Azure-SSIS IR) fornisce un'interfaccia per aggiungere i propri passaggi durante l'installazione o la riconfigurazione del runtime di integrazione Azure-SSIS. 

Utilizzando l'installazione personalizzata, è possibile modificare la configurazione operativa o l'ambiente predefinito per, ad esempio, avviare servizi Windows aggiuntivi, rendere persistenti le credenziali di accesso per le condivisioni file o utilizzare la crittografia avanzata/protocollo di rete più sicuro (TLS 1.2). In alternativa, è possibile installare componenti aggiuntivi, ad esempio assembly, driver o estensioni, in ogni nodo del runtime di runtime di runtime Azure-SSIS.

È possibile eseguire configurazioni personalizzate nel componente di ricreazione Azure-SSIS in uno dei due modi seguenti:You can do custom setups on your Azure-SSIS IR in either of two ways: 
* **Express installazione personalizzata senza uno script**: Eseguire alcune configurazioni di sistema comuni e comandi di Windows o installare alcuni componenti aggiuntivi popolari o consigliati senza utilizzare script.
* **Configurazione personalizzata standard con uno script:** preparare uno script e i file associati e caricarli tutti insieme in un contenitore BLOB nell'account di archiviazione di Azure.Standard custom setup with a script : Prepare a script and its associated files, and upload them all together to a blob container in your Azure storage account. È quindi possibile fornire un URI (Uniform Resource Identifier) di firma di accesso condiviso (SAS) per il contenitore quando si configura o si riconfigura il sistema di token di messaggistica apici Azure-SSIS. Ogni nodo del sistema di oggetti di messaggizione IR Azure-SSIS scarica quindi lo script e i file associati dal contenitore ed esegue l'installazione personalizzata con autorizzazioni elevate. Al termine dell'installazione personalizzata, ogni nodo carica l'output standard di esecuzione e altri log nel contenitore.

È possibile installare sia componenti gratuiti, senza licenza e componenti con licenza a pagamento con configurazioni personalizzate rapide e standard. Se si è un fornitore di software indipendente (ISV), vedere Sviluppare componenti a pagamento o con licenza per un runtime di accesso [Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Poiché i nodi della serie v2 di un componente di gestione delle dimensioni di Azure-SSIS non sono adatti per l'installazione personalizzata, usare invece i nodi della serie v3.Because v2-series nodes of an Azure-SSIS IR are not suitable for custom setup, use v3-series nodes instead. Se si utilizzano già nodi di serie v2, passare ai nodi della serie v3 il prima possibile.

## <a name="current-limitations"></a>Limitazioni correnti

Le seguenti limitazioni si applicano solo alle configurazioni personalizzate standard:

- Se si desidera utilizzare *gacutil.exe* nello script per installare assembly nella Global Assembly Cache (GAC), è necessario fornire *gacutil.exe* come parte dell'installazione personalizzata. In alternativa, è possibile utilizzare la copia fornita nel contenitore *Anteprima pubblica,* descritta più avanti nella sezione "Istruzioni".

- Se si desidera fare riferimento a una sottocartella nello script, `.\` *msiexec.exe* non supporta la notazione per fare riferimento alla cartella principale. Utilizzare un comando `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` come `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`instead di .

- Le condivisioni amministrative o le condivisioni di rete nascoste create automaticamente da Windows non sono attualmente supportate nel client di gestione Azure-SSIS.

- Il driver ODBC IBM iSeries Access non è supportato nel runtime di irgoria Azure-SSIS. Potrebbero verificarsi errori di installazione durante l'installazione personalizzata. In tal caso, contattare il supporto IBM per assistenza.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per personalizzare il raggio di azione di Azure-SSIS, sono necessari gli elementi seguenti:To customize your Azure-SSIS IR, you need the following items:

- [Una sottoscrizione di AzureAn Azure subscription](https://azure.microsoft.com/)

- [Eseguire il provisioning del runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un account di archiviazione](https://azure.microsoft.com/services/storage/)di Azure. Non richiesto per configurazioni personalizzate rapide. Per le configurazioni personalizzate standard, caricare e archiviare lo script di installazione personalizzato e i file associati in un contenitore BLOB. Il processo di installazione personalizzata carica nello stesso contenitore BLOB anche il log di esecuzione.

## <a name="instructions"></a>Istruzioni

1. Se si vuole configurare o riconfigurare il componente di accesso Azure-SSIS con PowerShell, scaricare e installare [Azure PowerShell.](/powershell/azure/install-az-ps) Per le configurazioni personalizzate rapide, andare al passaggio 4.For express custom setups, skip to step 4.

1. Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

   * È necessario disporre di un file di script denominato *main.cmd*, che è il punto di ingresso dell'installazione personalizzata.  
   * Per assicurarsi che lo script possa essere eseguito in modo invisibile all'utente, è consigliabile prima testarlo nel computer locale.  
   * Se si desidera caricare nel contenitore altri log generati da altri strumenti, ad esempio *msiexec.exe*, specificare la variabile di ambiente predefinita , `CUSTOM_SETUP_SCRIPT_LOG_DIR`come cartella di registro negli script, ad esempio *msiexec /i xxx.msi /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%%-install.log*.

1. Scaricare, installare e aprire Esplora archivi di [Azure.](https://storageexplorer.com/) A tale scopo, procedere come indicato di seguito:

   a. In **(Locale e collegato)** fare clic con il pulsante destro del mouse su **Account di**archiviazione e quindi scegliere Connetti ad Archiviazione di **Azure**.

      ![Connettersi ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selezionare **Usa un nome e una chiave dell'account di archiviazione**e quindi selezionare **Avanti**.

      ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Immettere il nome e la chiave dell'account di archiviazione di Azure, selezionare **Avanti**e quindi **Connessione**.

      ![Fornire il nome e la chiave dell'account di archiviazioneProvide storage account name and key](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Nell'account di archiviazione di Azure connesso fare clic con il pulsante destro del mouse su **Contenitori BLOB**, selezionare **Crea contenitore BLOB**e assegnare un nome al nuovo contenitore.

      ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare *main.cmd* al livello superiore del contenitore, non in alcuna cartella. Assicurarsi inoltre che il contenitore contenga solo i file di installazione personalizzati necessari, in modo che il download nel runtime di accesso Azure-SSIS in un secondo momento non richiederà molto tempo. La durata massima di un'installazione personalizzata è attualmente impostata su 45 minuti prima del timeout. Ciò include il tempo necessario per scaricare tutti i file dal contenitore e installarli nel runtime di accesso Azure-SSIS. Se l'installazione richiede più tempo, generare un ticket di supporto.

      ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Fare clic con il pulsante destro del mouse sul contenitore e quindi **scegliere Ottieni firma di accesso condiviso**.

      ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Creare l'URI di accesso condiviso per il contenitore con una scadenza sufficientemente lunga e con l'autorizzazione di lettura/scrittura/elenco. È necessario l'URI di accesso condiviso per scaricare ed eseguire lo script di installazione personalizzato e i file associati ogni volta che qualsiasi nodo del runtime di archiviazione Azure-SSIS viene riutilizzato o riavviato. Le autorizzazioni di scrittura sono necessarie per caricare i log di esecuzione dell'installazione.

      > [!IMPORTANT]
      > Assicurarsi che l'URI di accesso condiviso non scada e che le risorse di installazione personalizzate siano sempre disponibili durante l'intero ciclo di vita del raggio di gioco Azure-SSIS, dalla creazione all'eliminazione, soprattutto se si arresta e si avvia regolarmente il componente di accesso Azure-SSIS durante questo periodo.

      ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copiare e salvare l'URI SAS del contenitore.

      ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando si configura o si riconfigura il runtime di integrazione Azure-SSIS con un'interfaccia utente di Data Factory, è possibile aggiungere o rimuovere configurazioni personalizzate selezionando la casella di controllo Personalizza runtime integrazione **Azure-SSIS con configurazioni di sistema/installazioni di** componenti aggiuntive nella sezione **Impostazioni avanzate** del riquadro Installazione del runtime di **integrazione.** 

   Se si desidera aggiungere configurazioni personalizzate standard, immettere l'URI di accesso condiviso del contenitore nella casella URI di accesso condiviso del **contenitore di installazione personalizzato.** 
   
   Se si desidera aggiungere impostazioni personalizzate rapide, selezionare **Nuovo** per aprire il riquadro **Aggiungi impostazione personalizzata rapida** e quindi selezionare un tipo nell'elenco a discesa Tipo di installazione **personalizzata rapida:**

   * Se si seleziona il tipo di **comando Esegui cmdkey,** è possibile rendere persistenti le credenziali di accesso per le condivisioni file o le condivisioni File di Azure nel runtime di messaggi a i/R Azure-SSIS immettendo il nome o il nome di dominio di destinazione, il nome o il nome utente dell'account e la chiave o la password dell'account nelle caselle **/Aggiungi**, **/Utente**e **/Passa.** È simile all'esecuzione del comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) di Windows nel computer locale.
   
   * Se si seleziona il tipo Aggiungi variabile di **ambiente,** è possibile aggiungere variabili di ambiente Windows da usare nei pacchetti eseguiti nel runtime di messaggistica internazionale Azure-SSIS immettendo il nome e il valore della variabile di ambiente nelle caselle **Nome variabile** e **Valore variabile.** È simile all'esecuzione del comando [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) di Windows nel computer locale.

   * Se si seleziona il tipo di **componente Installa licenza,** è possibile selezionare un componente integrato dai partner ISV nell'elenco a discesa **Nome componente:**

     * Se si seleziona il componente Task Factory di **SentryOne,** è possibile installare la suite di componenti [Di Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) da SentryOne nel runtime di licenza Azure-SSIS immettendo il codice di licenza del prodotto acquistato nella casella **Codice di** licenza. L'attuale versione integrata è **2019.4.3**.

     * Se si seleziona il **oh22 HEDDA. IO,** è possibile installare il [HEDDA. ](https://hedda.io/ssis-component/)Componente di pulizia/qualità dei dati I/O da oh22 nel raggio di iR Azure-SSIS dopo l'acquisto del servizio. L'attuale versione integrata è **1.0.13**.

     * Se si seleziona il componente **SQLPhonetics.NET di oh22,** è possibile installare il componente di qualità/corrispondenza dei dati [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) da oh22 nel componente di gestione delle chiavi Azure-SSIS immettendo il codice di licenza del prodotto acquistato nella casella Codice **di licenza.** L'attuale versione integrata è **1.0.43**.

     * Se si seleziona il componente **SSIS Integration Toolkit di KingswaySoft,** è possibile installare la suite di connettori [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) per le app CRM/ERP/marketing/collaborazione, ad esempio Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud e così via da KingswaySoft nella classe Azure-SSIS IR immettendo la licenza del codice Product Key acquistata da tali applicazioni nella casella Del codice di **licenza.** L'attuale versione integrata è **2019.2**.

     * Se si seleziona il componente **SSIS Productivity Pack di KingswaySoft,** è possibile installare la suite di componenti [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) di KingswaySoft nel runtime di licenza Azure-SSIS immettendo il codice di licenza del prodotto acquistato da essi nella casella Codice di **licenza.** La versione integrata corrente è **10.0**.

     * Se si seleziona il componente Xtract IS di **Theobald Software,** è possibile installare la suite di connettori [Xtract IS](https://theobald-software.com/en/xtract-is/) per il sistema SAP (ERP, S/4HANA, BW) da Theobald Software nel componente iR Azure-SSIS trascinando & rilasciando/caricando il file di licenza del prodotto acquistato da essi nella casella **File di licenza.** L'attuale versione integrata è **6.1.1.3**.

   Le impostazioni personalizzate express aggiunte verranno visualizzate nella sezione **Impostazioni avanzate.** Per rimuoverle, selezionare le relative caselle di controllo e quindi selezionare **Elimina**.

   ![Impostazioni avanzate con configurazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando si configura o si riconfigura il componente di accesso Azure-SSIS con PowerShell, è possibile aggiungere o rimuovere le configurazioni personalizzate eseguendo il `Set-AzDataFactoryV2IntegrationRuntime` cmdlet prima di avviare il componente di accesso Azure-SSIS.
   
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
   
   Al termine dell'installazione personalizzata standard e all'avvio del sistema di conversione dei messaggi di accesso Azure-SSIS, è possibile trovare l'output standard di *main.cmd* e altri log di esecuzione nella cartella *main.cmd.log* del contenitore di archiviazione.

1. To view some samples of standard custom setups, connect to our Public Preview container by using Azure Storage Explorer.

   a. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

      ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selezionare Usa un URI di sicurezza e quindi nella casella URI immettere l'URI di accesso condiviso seguente:Select Use a **SAS URI** and then, in the **URI** box, enter the following SAS URI:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selezionare **Avanti**, quindi **Connessione**.

   d. Nel riquadro sinistro selezionare il contenitore **publicpreview** connesso e quindi fare doppio clic sulla cartella *CustomSetupScript.* Questa cartella contiene gli elementi seguenti:

      * Una cartella *di esempio,* che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo del componente di gestione Azure-SSIS. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene inoltre una cartella *gacutil,* il cui intero contenuto (*gacutil.exe*, *gacutil.exe.config*e *1033-gacutlrc.dll*) può essere copiato così com'è nel contenitore.

      * Una cartella *UserScenarios,* che contiene diversi esempi di installazione personalizzati da scenari utente reali.

        ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Fare doppio clic sulla cartella *UserScenarios* per trovare gli elementi seguenti:

      * Una cartella *.NET FRAMEWORK 3.5,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare una versione precedente di .NET Framework che potrebbe essere necessaria per i componenti personalizzati in ogni nodo del runtime di ir Azure-SSIS.

      * Una cartella *BCP,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare le utilità della riga di comando di SQL Server (*MsSqlCmdLnUtils.msi*), incluso il programma di copia di massa (*bcp*), in ogni nodo del menu a/ir Azure-SSIS.

      * Una cartella *EXCEL,* che contiene uno script di installazione personalizzato (*main.cmd*) per l'installazione di librerie e assembly di C, che è possibile usare nelle attività di script per leggere e scrivere dinamicamente i file di Excel in ogni nodo del runtime di ir Azure-SSIS. 
      
        Scaricare innanzitutto [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), quindi caricarli tutti insieme *a main.cmd* nel contenitore. In alternativa, se si desidera usare solo la gestione connessione di Excel standard, l'origine Excel e la destinazione di Excel, il componente ridistribuibile di accesso richiesto è già preinstallato nel raggio di irnaturale Azure-SSIS, pertanto non è necessaria alcuna configurazione personalizzata.
      
      * Una cartella *MYSQL ODBC,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC MySQL in ogni nodo del sistema di oggetti di irriboccupazione di Azure-SSIS. Questa installazione consente di utilizzare la gestione connessione ODBC, l'origine e la destinazione per connettersi al server MySQL. 
     
        Scaricare innanzitutto le versioni più recenti a [64 bit e a 32 bit dei programmi di installazione dei driver ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (ad esempio, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi),* quindi caricarli tutti insieme a *main.cmd* nel contenitore.

      * Una cartella *ORACLE ENTERPRISE,* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di configurazione dell'installazione invisibile all'utente (*client.rsp*) per installare i connettori Oracle e il driver OCI in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa configurazione consente di utilizzare Oracle Connection Manager, l'origine e la destinazione per connettersi al server Oracle. 
      
        Scaricare innanzitutto Microsoft Connectors v5.0 per Oracle (*AttunitySSSISOraAdaptersSetup.msi* e *AttunitySSSIsOraAdaptersSetup64.msi*) dall'Area [download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e il client Oracle più recente (ad *esempio, winx64_12102_client.zip*) da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), quindi caricarli tutti insieme a *main.cmd* e *client.rsp* nel contenitore. Se si utilizza TNS per connettersi a Oracle, è inoltre necessario scaricare *tnsnames.ora*, modificarlo e caricarlo nel contenitore, in modo che possa essere copiato nella cartella di installazione di Oracle durante l'installazione.

      * Una cartella *di ADO.NET ORACLE STANDARD,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver Oracle ODP.NET in ogni nodo del sistema di oggetti di irribita Azure-SSIS. Questa configurazione consente di utilizzare la ADO.NET Connection Manager, origine e destinazione per connettersi al server Oracle. 
      
        Scaricare innanzitutto [il driver di ODP.NET Oracle più recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) ( ad *esempio, ODP.NET_Managed_ODAC122cR1.zip*), quindi caricarlo con *main.cmd* nel contenitore.
       
      * Una cartella *ODBC ORACLE STANDARD,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver ODBC Oracle e configurare il nome dell'origine dati (DSN) in ogni nodo del catalogo mentitermini Azure-SSIS. Questa configurazione consente di utilizzare Gestione connessione ODBC, origine e destinazione o Gestione connessione Power Query e l'origine con il tipo di origine dati ODBC per connettersi al server Oracle. 
      
        Scaricare innanzitutto il client istantaneo Oracle (pacchetto di base o pacchetto Lite di base) e il pacchetto ODBC, quindi caricarli tutti insieme a *main.cmd* nel contenitore:
        * Scaricare pacchetti [a 64 bit](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacchetto di base: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Pacchetto Lite di base: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * Download di [pacchetti a 32 bit](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacchetto di base: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Pacchetto Lite di base: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacchetto ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Una cartella *OLEDB ORACLE STANDARD,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare il driver Oracle OLEDB in ogni nodo del sistema di oggetti di irterminio Azure-SSIS. Questa configurazione consente di utilizzare Gestione connessione OLEDB, origine e destinazione per connettersi al server Oracle. 
     
        Scaricare innanzitutto [il driver OLEDB Oracle più recente](https://www.oracle.com/partners/campaign/index-090165.html) (ad esempio, *ODAC122010Xcopy_x64.zip),* quindi caricarlo con *main.cmd* nel contenitore.

      * Una cartella *ODBC POSTGRESQL,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare i driver ODBC PostgreSQL in ogni nodo del sistema di oggetti di irlivello IR Azure-SSIS. Questa configurazione consente di utilizzare Gestione connessione ODBC, l'origine e la destinazione per connettersi al server PostgreSQL. 
     
        Scaricare innanzitutto le versioni più recenti a [64 bit e a 32 bit dei programmi di installazione dei driver ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (ad esempio, *psqlodbc_x64.msi* e *psqlodbc_x86.msi),* quindi caricarli tutti insieme a *main.cmd* nel contenitore.

      * Una cartella *SAP BW,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare l'assembly del connettore SAP .NET (*librfc32.dll*) in ogni nodo della versione Enterprise iR di Azure-SSIS. Questa configurazione consente di utilizzare la gestione connessione SAP Business Warehouse (BW), l'origine e la destinazione per connettersi al server SAP BW. 
      
        Innanzitutto, caricare la versione a 64 bit o la versione a 32 bit di *librfc32.dll* dalla cartella di installazione SAP con *main.cmd* nel contenitore. Lo script consente di copiare quindi l'assembly SAP nella cartella *%windir% , SysWow64* o *%windir% , System32* durante l'installazione.

      * Una cartella *STORAGE,* che contiene uno script di installazione personalizzato (*main.cmd*) per installare Azure PowerShell in ogni nodo del componente di accesso Azure-SSIS. Questa configurazione consente di distribuire ed eseguire pacchetti SSIS che eseguono script di [PowerShell per modificare l'account di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)di Azure. 
      
        Copiare *main.cmd*, un *esempio di AzurePowerShell.msi* (o usare la versione più recente) e *storage.ps1* nel contenitore. Usare *PowerShell.dtsx* come modello per i pacchetti. Il modello di pacchetto combina [un'attività](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)di download BLOB di Azure, che scarica *storage.ps1* come script di PowerShell modificabile, e un'attività [Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), che esegue lo script in ogni nodo.

      * Una cartella *TERADATA,* che contiene uno script di installazione personalizzato (*main.cmd*), il file associato (*install.cmd*) e i pacchetti del programma di installazione (*.msi*). Questi file installano i connettori Teradata, l'API Teradata Parallel Transporter (TPT) e il driver ODBC in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa configurazione consente di utilizzare Gestione connessione Teradata, origine e destinazione per connettersi al server Teradata. 
      
        Scaricare innanzitutto [il file zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (ad esempio, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* quindi caricarlo insieme ai file *cmd* e *MSI* menzionati in precedenza nel contenitore.

      * Una cartella *TLS 1.2,* che contiene uno script di installazione personalizzato (*main.cmd*) per usare crittografia avanzata/protocollo di rete più sicuro (TLS 1.2) e disabilitare le versioni SSL/TLS meno recenti in ogni nodo del token di ir Azure-SSIS.

      * Una cartella *OPENJDK,* che contiene uno script di installazione personalizzato (*main.cmd*) e un file di PowerShell (*install_openjdk.ps1*) per installare l'openJDK di zuo, in ogni nodo del file IR Azure-SSIS. Questa configurazione consente di usare i connettori Azure Data Lake Store e Flexible File per elaborare i file ORC e Parquet. Per altre informazioni, vedere [Azure Feature Pack per Integration Services.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) 
      
        In primo luogo, [scaricare l'ultima OpenJDK di zulu](https://www.azul.com/downloads/zulu/zulu-windows/) (ad esempio, *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* quindi caricarlo insieme a *main.cmd* e *install_openjdk.ps1* nel contenitore.

        ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Per provare questi esempi di configurazione personalizzati, copiare il contenuto dalla cartella selezionata nel contenitore.
   
      Quando si configura o si riconfigura il runtime di integrazione Azure-SSIS tramite l'interfaccia utente di Data Factory, selezionare la casella di controllo Personalizza il runtime di **integrazione Azure-SSIS con configurazioni di sistema/installazioni di componenti aggiuntivi** nella sezione **Impostazioni avanzate** e quindi immettere l'URI della chiamata in classe di accesso condiviso del contenitore nella casella **URI sAS del contenitore** di installazione personalizzato.
   
      Quando si configura o si riconfigura il runtime di accesso `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS con PowerShell, eseguire `SetupScriptContainerSasUri` il cmdlet con l'URI di accesso condiviso del contenitore come valore per il parametro.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'edizione Enterprise del runtime di integrazione Azure-SSISSet up the Enterprise Edition of the Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Sviluppare componenti personalizzati a pagamento o con licenza per il runtime di integrazione Azure-SSISDevelop paid or licensed custom components for the Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)