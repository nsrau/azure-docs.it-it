---
title: Personalizzare l'installazione del runtime di integrazione Azure-SSIS
description: Questo articolo descrive come usare l'interfaccia di installazione personalizzata per il runtime di integrazione Azure-SSIS per installare componenti aggiuntivi o cambiare le impostazioni.
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
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964550"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personalizzare l'installazione del runtime di integrazione Azure-SSIS

L'interfaccia di installazione personalizzata per il runtime di integrazione Azure-SSIS offre un'interfaccia per l'aggiunta di passaggi di installazione durante il provisioning o la riconfigurazione del runtime di integrazione Azure-SSIS. 

L'installazione personalizzata consente di modificare la configurazione operativa o l'ambiente predefiniti, ad esempio per avviare servizi di Windows aggiuntivi o per salvare in modo permanente le credenziali di accesso per le condivisioni di file, oppure di installare componenti aggiuntivi, come assembly, driver o estensioni, in ogni nodo del runtime di integrazione Azure-SSIS.

Esistono due modi per eseguire le configurazioni personalizzate nell'Azure-SSIS IR: le configurazioni personalizzate Express senza script e le configurazioni standard personalizzate con gli script.

Con le installazioni personalizzate rapide, è possibile eseguire alcuni comandi comuni di configurazione di sistema/Windows oppure installare alcuni componenti aggiuntivi popolari/consigliati senza usare alcuno script.  

Con le configurazioni personalizzate standard, è necessario preparare uno script e i file associati e caricarli tutti insieme in un contenitore BLOB nell'account di archiviazione di Azure. È quindi necessario specificare una firma di accesso condiviso (SAS) Uniform Resource Identifier (URI) per il contenitore durante il provisioning o la riconfigurazione del Azure-SSIS IR. Ogni nodo del Azure-SSIS IR scaricherà lo script e i file associati dal contenitore ed eseguirà l'installazione personalizzata con privilegi elevati. Al termine dell'installazione personalizzata, ogni nodo caricherà l'output standard dell'esecuzione e gli altri log nel contenitore.

Con le configurazioni personalizzate Express/standard, è possibile installare i componenti sia gratuiti che senza licenza e a pagamento/con licenza. Se si è un ISV, vedere la documentazione su [come sviluppare componenti a pagamento o con licenza per Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Poiché i nodi della serie v2 di Azure-SSIS IR non sono adatti per l'installazione personalizzata, usare invece i nodi della serie v3.  Se si usano già i nodi della serie v2, passare ai nodi della serie v3 appena possibile.

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti si applicano solo alle configurazioni personalizzate standard:

- Se si desidera utilizzare `gacutil.exe` nello script per installare gli assembly nella global assembly cache (GAC), è necessario fornire `gacutil.exe` come parte dell'installazione personalizzata oppure utilizzare la copia fornita nel contenitore di anteprima pubblica riportata di seguito.

- Se si vuole fare riferimento a una sottocartella nello script, `msiexec.exe` non supporta la notazione `.\` usata per i riferimenti alla cartella radice. Usare un comando simile a `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` invece di `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Le condivisioni amministrative, ad esempio le condivisioni di rete nascoste create automaticamente da Windows, non sono attualmente supportate nel Azure-SSIS IR.

- Il driver ODBC di IBM iSeries Access non è supportato nel Azure-SSIS IR. È possibile che vengano visualizzati errori di installazione durante l'installazione personalizzata. Per assistenza, contattare il supporto tecnico IBM.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per personalizzare il runtime di integrazione Azure-SSIS occorre quanto segue:

- [Sottoscrizione di Azure](https://azure.microsoft.com/)

- [Provisioning del Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un account di archiviazione di Azure](https://azure.microsoft.com/services/storage/). Non richiesto per le configurazioni personalizzate rapide. Per le configurazioni personalizzate standard, è possibile caricare e archiviare lo script di installazione personalizzato e i file associati in un contenitore BLOB. Il processo di installazione personalizzata carica nello stesso contenitore BLOB anche il log di esecuzione.

## <a name="instructions"></a>Istruzioni

1. Se si vuole eseguire il provisioning o riconfigurare la Azure-SSIS IR con PowerShell, scaricare e installare [Azure PowerShell](/powershell/azure/install-az-ps). Per le configurazioni personalizzate rapide, andare al passaggio 4.

1. Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

   1. È necessario disporre di un file di script denominato `main.cmd`, ovvero il punto di ingresso dell'installazione personalizzata.

   1. È necessario assicurarsi che lo script possa essere eseguito in modalità invisibile all'utente, è consigliabile testare prima lo script sul computer locale.

   1. Se si vuole che nel contenitore vengano caricati anche i log generati da altri strumenti (ad esempio `msiexec.exe`), specificare negli script la variabile di ambiente predefinita `CUSTOM_SETUP_SCRIPT_LOG_DIR` come cartella dei log (ad esempio `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Scaricare, installare e avviare [Azure Storage Explorer](https://storageexplorer.com/).

   1. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure).

      ![Connessione ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e selezionare **Avanti**.

      ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Immettere il nome dell'account di archiviazione di Azure e la chiave, selezionare **Avanti**, quindi selezionare **Connetti**.

      ![Fornire il nome e la chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Nell'account di archiviazione di Azure connesso fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB), scegliere **Create Blob Container** e assegnare un nome al nuovo contenitore.

      ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare `main.cmd` nel primo livello del contenitore, non in una cartella qualsiasi. Assicurarsi anche che il contenitore includa solo i file di installazione personalizzata necessari, in modo che non sarà necessario troppo tempo per scaricarli in Azure-SSIS IR in un secondo momento. Il periodo massimo per la configurazione personalizzata è attualmente impostato su 45 minuti prima del timeout e include il tempo necessario per scaricare tutti i file dal contenitore e installarli nel Azure-SSIS IR. Se è necessario un periodo più lungo, generare un ticket di supporto.

      ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Fare clic con il pulsante destro del mouse sul contenitore e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

      ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Creare l'URI SAS per il contenitore con una scadenza sufficientemente lunga e con autorizzazioni di lettura, scrittura ed elenco. L'URI SAS è necessario per scaricare ed eseguire lo script di installazione personalizzato e i file associati ogni volta che viene ricreata l'immagine di un nodo del runtime di integrazione Azure-SSIS o viene riavviato. Le autorizzazioni di scrittura sono necessarie per caricare i log di esecuzione dell'installazione.

      > [!IMPORTANT]
      > Verificare che l'URI di firma di accesso condiviso non scada e che le risorse di installazione personalizzate siano sempre disponibili durante l'intero ciclo di vita del Azure-SSIS IR, dalla creazione all'eliminazione, soprattutto se si arresta e si avvia regolarmente il Azure-SSIS IR durante questo periodo.

      ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Copiare e salvare l'URI SAS del contenitore.

      ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando si esegue il provisioning o si riconfigurano i Azure-SSIS IR con Data Factory interfaccia utente, è possibile aggiungere o rimuovere le configurazioni personalizzate selezionando la casella di controllo **Personalizza il Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema e le installazioni dei componenti** nella sezione **Impostazioni avanzate** del pannello di installazione di Integration Runtime. 

   Se si desidera aggiungere configurazioni personalizzate standard, immettere l'URI SAS del contenitore nel campo URI di firma di accesso condiviso del **contenitore personalizzato** . 
   
   Se si desidera aggiungere impostazioni personalizzate rapide, selezionare **nuovo** per aprire il pannello **Aggiungi installazione personalizzata rapida** e quindi selezionare tutti i tipi nel menu a discesa **tipo di installazione personalizzata Express** :

   1. Se si seleziona il tipo di **comando Run cmdkey** , è possibile rendere permanente le credenziali di accesso per le condivisioni file/File di Azure in Azure-SSIS IR immettendo rispettivamente il nome del computer/dominio, il nome dell'account/nome utente e la chiave dell'account/password nei campi **/Add**, **/User**e **/pass** . Questa operazione è simile all'esecuzione del comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) di Windows sul computer locale.
   
   1. Se si seleziona il tipo di **variabile di ambiente Aggiungi** , è possibile aggiungere le variabili di ambiente Windows da usare nei pacchetti eseguiti su Azure-SSIS IR immettendo il nome e il valore della variabile di ambiente rispettivamente nei campi **nome** variabile e **valore variabile** . Questa operazione è simile all'esecuzione del comando Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) nel computer locale.

   1. Se si seleziona il tipo di **componente installa con licenza** , è possibile selezionare tutti i componenti integrati dai partner ISV nel menu a discesa **nome componente** :

      1. Se si seleziona il componente della **Factory delle attività di SentryOne** , è possibile installare la suite di factory delle [attività](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) dei componenti da SentryOne nel Azure-SSIS IR immettendo il codice di licenza del prodotto acquistato dall'utente nel campo **chiave di licenza** . La versione integrata corrente è **2019.4.3**.

      1. Se si seleziona il **Hedda oh22's. Componente IO** , è possibile installare [Hedda. ](https://hedda.io/ssis-component/)Componente per la qualità dei dati e la pulizia dei dati di i/o da oh22 nel Azure-SSIS IR dopo l'acquisto del servizio. La versione integrata corrente è **1.0.13**.

      1. Se si seleziona il componente **SQLPhonetics.NET di oh22's** , è possibile installare il componente [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data Quality/matching da oh22 nel Azure-SSIS IR immettendo il codice Product Key acquistato da essi nel campo **chiave di licenza** . La versione integrata corrente è **1.0.43**.
   
   Le impostazioni di aggiunta personalizzate Express verranno visualizzate nella sezione **Impostazioni avanzate** . Per rimuoverli, è possibile selezionare le caselle di controllo e quindi selezionare **Elimina**.

   ![Impostazioni avanzate con configurazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando si esegue il provisioning o si riconfigura la Azure-SSIS IR con PowerShell, è possibile aggiungere o rimuovere le configurazioni personalizzate eseguendo il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` prima di avviare il Azure-SSIS IR.
   
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
   
   Al termine dell'installazione personalizzata standard e la Azure-SSIS IR viene avviata, è possibile trovare l'output standard di `main.cmd` e altri log di esecuzione nella cartella `main.cmd.log` del contenitore di archiviazione.

1. Per visualizzare alcuni esempi di configurazioni standard personalizzate, connettersi al contenitore di anteprima pubblica con Azure Storage Explorer.

   1. In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

      ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Selezionare **Use a SAS URI**(Usare un URI di firma di accesso condiviso) e immettere l'URI SAS seguente per il contenitore dell'anteprima pubblica. Selezionare **Avanti**, quindi selezionare **Connetti**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Selezionare il contenitore dell'anteprima pubblica connesso e fare doppio clic sulla cartella `CustomSetupScript`. Questa cartella contiene gli elementi seguenti:

      1. Una cartella `Sample`, che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo del runtime di integrazione Azure-SSIS. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene inoltre una cartella `gacutil`, l'intero contenuto della quale (`gacutil.exe`, `gacutil.exe.config` e `1033\gacutlrc.dll`) può essere copiato così com'è nel contenitore.

      1. Una cartella `UserScenarios`, che contiene diversi esempi di installazione personalizzata da scenari utente reali.

      ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Fare doppio clic sulla cartella `UserScenarios` per trovare gli elementi seguenti:

      1. Una cartella `.NET FRAMEWORK 3.5`, che contiene un'installazione personalizzata per installare una versione precedente di .NET Framework che potrebbe essere necessaria per i componenti personalizzati in ogni nodo del runtime di integrazione Azure-SSIS.

      1. Una cartella `BCP`, che contiene un'installazione personalizzata per installare utilità della riga di comando di SQL Server (`MsSqlCmdLnUtils.msi`), tra cui il programma di copia bulk (`bcp`), in ogni nodo del runtime di integrazione Azure-SSIS.

      1. Una cartella `EXCEL`, che contiene uno script di installazione personalizzato (`main.cmd`) per C# installare gli assembly e le librerie che è possibile usare nelle attività script per leggere/scrivere in modo dinamico i file di Excel in ogni nodo della Azure-SSIS IR. Prima di tutto, scaricare `ExcelDataReader.dll` da [qui](https://www.nuget.org/packages/ExcelDataReader/) e `DocumentFormat.OpenXml.dll` da [qui](https://www.nuget.org/packages/DocumentFormat.OpenXml/), quindi caricarli tutti insieme al `main.cmd` nel contenitore. In alternativa, se si desidera solo utilizzare la gestione connessione/origine/destinazione standard di Excel, il ridistribuibile di accesso richiesto è già preinstallato nel Azure-SSIS IR, pertanto non è necessaria alcuna configurazione personalizzata.
      
      1. Una cartella `MYSQL ODBC`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare i driver ODBC MySQL in ogni nodo della Azure-SSIS IR. Questa installazione consente di usare la gestione connessione ODBC/origine/destinazione per connettersi al server MySQL. Scaricare prima di tutto le versioni più recenti a 64 bit e a 32 bit dei programmi di installazione dei driver ODBC MySQL, ad esempio `mysql-connector-odbc-8.0.13-winx64.msi` e `mysql-connector-odbc-8.0.13-win32.msi` da [MySQL](https://dev.mysql.com/downloads/connector/odbc/), quindi caricarli tutti insieme ai `main.cmd` nel contenitore.

      1. Una cartella `ORACLE ENTERPRISE`, che contiene uno script di installazione personalizzato (`main.cmd`) e un file di configurazione per l'installazione invisibile all'utente (`client.rsp`) dei connettori e del driver OCI di Oracle in ogni nodo di Azure-SSIS IR Enterprise Edition. Questa installazione consente di utilizzare la gestione connessione/origine/destinazione Oracle per la connessione al server Oracle. Scaricare prima i connettori Microsoft v5.0 per Oracle (`AttunitySSISOraAdaptersSetup.msi` e `AttunitySSISOraAdaptersSetup64.msi`) dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e il client Oracle più recente, ad esempio `winx64_12102_client.zip`, da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) e quindi caricarli tutti insieme con `main.cmd` e `client.rsp` nel contenitore. Se si usa TNS per la connessione a Oracle, è necessario anche scaricare `tnsnames.ora`, modificarlo e caricarlo nel contenitore, in modo che possa essere copiato nella cartella di installazione di Oracle durante l'installazione.

      1. Una cartella`ORACLE STANDARD ADO.NET`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare il driver ODP.NET di Oracle in ogni nodo del runtime di integrazione Azure-SSIS. Questa installazione consente di usare la gestione connessione ADO.NET/origine/destinazione per connettersi al server Oracle. Prima di tutto, scaricare il driver Oracle ODP.NET più recente, ad esempio `ODP.NET_Managed_ODAC122cR1.zip` da [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), quindi caricarlo insieme a `main.cmd` nel contenitore.
       
      1. Una cartella `ORACLE STANDARD ODBC`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare il driver ODBC di Oracle e configurare DSN in ogni nodo di Azure-SSIS IR. Questa installazione consente di usare ODBC Connection Manager/Source/Destination o Power Query Connection Manager/Source con il tipo di origine dati ODBC per connettersi al server Oracle. Per prima cosa, scaricare la versione più recente di Oracle Instant client (pacchetto di base o pacchetto Lite di base) e il pacchetto ODBC, ad esempio i pacchetti a 64 bit da [qui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacchetto di base: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, pacchetto Lite di base: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pacchetto odbc: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) o pacchetti a 32 bit da [qui](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacchetto di base: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, pacchetto lite di base: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pacchetto ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), quindi caricarli tutti insieme con `main.cmd` nel contenitore.

      1. Una cartella `ORACLE STANDARD OLEDB`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare il driver Oracle OLEDB in ogni nodo della Azure-SSIS IR. Questa installazione consente di usare la gestione connessione OLEDB/origine/destinazione per connettersi al server Oracle. Scaricare prima di tutto il driver Oracle OLEDB più recente, ad esempio `ODAC122010Xcopy_x64.zip` da [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), quindi caricarlo insieme a `main.cmd` nel contenitore.

      1. Una cartella `POSTGRESQL ODBC`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare i driver ODBC PostgreSQL in ogni nodo della Azure-SSIS IR. Questa installazione consente di usare la gestione connessione ODBC/origine/destinazione per connettersi al server PostgreSQL. Scaricare prima di tutto le versioni più recenti a 64 bit e a 32 bit dei programmi di installazione del driver ODBC PostgreSQL, ad esempio `psqlodbc_x64.msi` e `psqlodbc_x86.msi` da [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/), quindi caricarli tutti insieme ai `main.cmd` nel contenitore.

      1. Una cartella `SAP BW`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare l'assembly di SAP .NET Connector (`librfc32.dll`) in ogni nodo della Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione SAP BW/origine/destinazione per connettersi a SAP BW Server. Per prima cosa, caricare la versione a 64 bit o a 32 bit di `librfc32.dll` dalla cartella di installazione di SAP insieme al `main.cmd` nel contenitore. Lo script copia quindi l'assembly SAP nella cartella `%windir%\SysWow64` o `%windir%\System32` durante l'installazione.

      1. Una cartella `STORAGE`, che contiene un'installazione personalizzata per installare Azure PowerShell in ogni nodo del runtime di integrazione Azure-SSIS. Questa installazione consente di distribuire ed eseguire pacchetti SSIS che eseguono [script di PowerShell per la gestione dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copiare `main.cmd`, un `AzurePowerShell.msi` di esempio (o usare la versione più recente) e `storage.ps1` al contenitore. Usare PowerShell.dtsx come modello per i pacchetti. Il modello di pacchetto combina un'[attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), che scarica `storage.ps1` come script di PowerShell modificabile, e un'[attività Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), che esegue lo script in ogni nodo.

      1. Una cartella `TERADATA`, che contiene uno script di installazione personalizzato (`main.cmd`), il file associato (`install.cmd`) e i pacchetti di installazione (`.msi`). Questi file installano i connettori Teradata, l'API TPT e il driver ODBC in ogni nodo della Azure-SSIS IR Enterprise Edition. Questa installazione consente di usare la gestione connessione/origine/destinazione Teradata per connettersi al server Teradata. Per prima cosa, scaricare il file zip strumenti e utilità Teradata 15. x, ad esempio `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`, da [Teradata](http://partnerintelligence.teradata.com), quindi caricarlo insieme ai `.cmd` e `.msi` file precedenti nel contenitore.

      1. Una cartella `ZULU OPENJDK`, che contiene uno script di installazione personalizzato (`main.cmd`) e un file di PowerShell (`install_openjdk.ps1`) per installare Zulu OpenJDK in ogni nodo della Azure-SSIS IR. Questa configurazione consente di usare connettori di file Azure Data Lake Store/flessibili per elaborare i file ORC/parquet. vedere [qui](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) per altre informazioni. Prima di tutto, scaricare la versione più recente di Zulu OpenJDK, ad esempio `zulu8.33.0.1-jdk8.0.192-win_x64.zip`, da [qui](https://www.azul.com/downloads/zulu/zulu-windows/), quindi caricarla insieme a `main.cmd` e `install_openjdk.ps1` nel contenitore.

      ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Per provare questi esempi di installazione personalizzata, copiare e incollare il contenuto dalla cartella selezionata nel contenitore.
   
      Quando si esegue il provisioning o si riconfigurano i Azure-SSIS IR con Data Factory interfaccia utente, selezionare la casella di controllo **Personalizza il Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema/installazioni dei componenti** nella sezione **Impostazioni avanzate** e immettere l'URI SAS del contenitore nel campo URI di firma di accesso condiviso del **contenitore personalizzato** .
   
      Quando si esegue il provisioning o si riconfigurano i Azure-SSIS IR con PowerShell, eseguire il cmdlet `Set-AzDataFactoryV2IntegrationRuntime` con l'URI SAS del contenitore come valore per il parametro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Passaggi successivi

-   [Enterprise Edition del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Come sviluppare componenti personalizzati a pagamento o concessi in licenza per il runtime di integrazione Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
