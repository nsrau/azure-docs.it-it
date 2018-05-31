---
title: Installazione personalizzata per il runtime di integrazione Azure-SSIS | Microsoft Docs
description: Questo articolo descrive come usare l'interfaccia di installazione personalizzata per il runtime di integrazione Azure-SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: b377b5ca9d46d66fe99a8f60383076920b098a7d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33769716"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Installazione personalizzata per il runtime di integrazione Azure-SSIS

L'interfaccia di installazione personalizzata per il runtime di integrazione Azure-SSIS consente di modificare la configurazione operativa o l'ambiente predefiniti, ad esempio per avviare servizi di Windows aggiuntivi, oppure di installare componenti aggiuntivi, come assembly, driver o estensioni, in ogni nodo del runtime di integrazione Azure-SSIS. In generale, offre un'interfaccia per l'aggiunta di passaggi di installazione durante il provisioning o la riconfigurazione del runtime di integrazione Azure-SSIS.

L'installazione personalizzata si configura preparando uno script e i relativi file associati e caricandoli in un contenitore BLOB nell'account di archiviazione di Azure. Quando si eseguono il provisioning o la riconfigurazione del runtime di integrazione Azure-SSIS è necessario fornire un URI (Uniform Resource Identifier) di firma di accesso condiviso (SAS, Shared Access Signature). Ogni nodo del runtime di integrazione Azure-SSIS scaricherà lo script e i file associati dal contenitore ed eseguirà l'installazione personalizzata con privilegi elevati. Al termine dell'installazione personalizzata, ogni nodo caricherà l'output standard dell'esecuzione e gli altri log nel contenitore.

È possibile installare sia componenti gratuiti o senza licenza, sia componenti a pagamento o concessi in licenza. I fornitori di software indipendenti possono vedere l'articolo [Come sviluppare componenti personalizzati a pagamento o concessi in licenza per il runtime di integrazione Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Limitazioni correnti

-   Se si desidera usare `gacutil.exe` per installare assembly nella Global Assembly Cache (GAC), è necessario specificarlo durante il programma di installazione personalizzato oppure usare la copia fornita nel contenitore di anteprima pubblica.

-   Se è necessario aggiungere il runtime di integrazione Azure-SSIS con installazione personalizzata a una rete virtuale, tenere presente che sono supportate solo le reti virtuali di Azure Resource Manager. Le reti virtuali classiche non sono supportate.

## <a name="prerequisites"></a>prerequisiti

Per personalizzare il runtime di integrazione Azure-SSIS occorre quanto segue:

-   [Sottoscrizione di Azure](https://azure.microsoft.com/)

-   [Un database SQL di Azure o un'istanza gestita di database SQL](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Eseguire il provisioning del runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Un account di archiviazione di Azure](https://azure.microsoft.com/services/storage/). Per l'installazione personalizzata, è necessario caricare e archiviare lo script di installazione personalizzato e i file associati in un contenitore BLOB. Il processo di installazione personalizzata carica nello stesso contenitore BLOB anche il log di esecuzione.

## <a name="instructions"></a>Istruzioni

2.  Scaricare e installare [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) versione 5.4 o successiva.

3.  Preparare lo script di installazione personalizzato e i file associati (ad esempio file con estensione bat, cmd, exe, dll, msi o ps1).

    1.  È necessario disporre di un file di script denominato `main.cmd`, ovvero il punto di ingresso dell'installazione personalizzata.

    2.  Se si vuole che nel contenitore vengano caricati anche i log generati da altri strumenti (ad esempio `msiexec.exe`), specificare negli script la variabile di ambiente predefinita `CUSTOM_SETUP_SCRIPT_LOG_DIR` come cartella dei log (ad esempio `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Scaricare, installare e avviare [Azure Storage Explorer](http://storageexplorer.com/).

    1.  In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure).

       ![Connessione ad Archiviazione di Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e selezionare **Avanti**.

       ![Usare un nome e una chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Immettere il nome dell'account di archiviazione di Azure e la chiave, selezionare **Avanti**, quindi selezionare **Connetti**.

       ![Fornire il nome e la chiave dell'account di archiviazione](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  Nell'account di archiviazione di Azure connesso fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB), scegliere **Create Blob Container** e assegnare un nome al nuovo contenitore.

       ![Creare un contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Selezionare il nuovo contenitore e caricare lo script di installazione personalizzato e i file associati. Assicurarsi di caricare `main.cmd` nel primo livello del contenitore, non in una cartella qualsiasi. 

       ![Caricare i file nel contenitore BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Fare clic con il pulsante destro del mouse sul contenitore e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

       ![Ottenere la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Creare l'URI SAS per il contenitore con una scadenza sufficientemente lunga e con autorizzazioni di lettura, scrittura ed elenco. L'URI SAS è necessario per scaricare ed eseguire lo script di installazione personalizzato e i file associati ogni volta che viene ricreata l'immagine di un nodo del runtime di integrazione Azure-SSIS. Le autorizzazioni di scrittura sono necessarie per caricare i log di esecuzione dell'installazione.

       ![Generare la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Copiare e salvare l'URI SAS del contenitore.

       ![Copiare e salvare la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Quando si riconfigura o si esegue il provisioning del runtime di integrazione Azure-SSIS con PowerShell, prima di avviare il runtime eseguire il cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` inserendo l'URI SAS del contenitore come valore per il nuovo parametro `SetupScriptContainerSasUri`. Ad esempio: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Al termine dell'installazione personalizzata e dopo l'avvio del runtime di integrazione Azure-SSIS, l'output standard di `main.cmd` e gli altri log di esecuzione saranno disponibili nella cartella `main.cmd.log` del contenitore di archiviazione.

2.  Per altri esempi di installazione personalizzata, connettersi al contenitore dell'anteprima pubblica con Azure Storage Explorer.

    a.  In **(Local and Attached)** (Locale e collegato) fare clic con il pulsante destro del mouse su **Account di archiviazione** e scegliere **Connect to Azure storage** (Connetti ad archiviazione di Azure), selezionare **Use a connection string or a shared access signature URI** (Usa una stringa di connessione o un URI di firma di accesso condiviso), quindi selezionare **Avanti**.

       ![Eseguire la connessione ad Archiviazione di Azure con la firma di accesso condiviso](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Selezionare **Use a SAS URI**(Usare un URI di firma di accesso condiviso) e immettere l'URI SAS seguente per il contenitore dell'anteprima pubblica. Selezionare **Avanti** e quindi **Connetti**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Fornire la firma di accesso condiviso per il contenitore](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Selezionare il contenitore dell'anteprima pubblica connesso e fare doppio clic sulla cartella `CustomSetupScript`. Questa cartella contiene gli elementi seguenti:

       1. Una cartella `Sample`, che contiene un'installazione personalizzata per installare un'attività di base in ogni nodo del runtime di integrazione Azure-SSIS. L'attività non esegue alcuna operazione se non andare in sospensione per pochi secondi. La cartella contiene inoltre una cartella `gacutil` che contiene `gacutil.exe`.

       2. Una cartella `UserScenarios`, che contiene più installazioni personalizzate per scenari utente reali.

    ![Contenuto del contenitore dell'anteprima pubblica](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Fare doppio clic sulla cartella `UserScenarios`. Questa cartella contiene gli elementi seguenti:

       1. Una cartella `.NET FRAMEWORK 3.5`, che contiene un'installazione personalizzata per installare una versione precedente di .NET Framework che potrebbe essere necessaria per i componenti personalizzati in ogni nodo del runtime di integrazione Azure-SSIS.

       2. Una cartella `BCP`, che contiene un'installazione personalizzata per installare utilità della riga di comando di SQL Server (`MsSqlCmdLnUtils.msi`), tra cui il programma di copia bulk (`bcp`), in ogni nodo del runtime di integrazione Azure-SSIS.

       3. Una cartella `EXCEL`, che contiene un'installazione personalizzata per installare assembly open source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` e `ExcelDataReader.dll`) in ogni nodo del runtime di integrazione Azure-SSIS.

       4. Una cartella `MSDTC`, che contiene un'installazione personalizzata per modificare le configurazioni di rete e di sicurezza per l'istanza Microsoft Distributed Transaction Coordinator (MSDTC) in ogni nodo del runtime di integrazione Azure-SSIS.

       5. Una cartella `ORACLE ENTERPRISE`, che contiene un'installazione personalizzata (`main.cmd`) e un file di configurazione per l'installazione invisibile all'utente (`client.rsp`) del driver OCI di Oracle in ogni nodo del runtime di integrazione Azure-SSIS Enterprise Edition (anteprima privata). Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Oracle. Prima di tutto è necessario scaricare `winx64_12102_client.zip` da [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) e quindi caricarlo insieme a `main.cmd` e `client.rsp` nel contenitore. Se si usa TNS per la connessione a Oracle, è necessario anche scaricare `tnsnames.ora`, modificarlo e caricarlo nel contenitore, in modo che possa essere copiato nella cartella di installazione di Oracle durante l'installazione.

       6. Una cartella`ORACLE STANDARD`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare il driver ODP.NET di Oracle in ogni nodo del runtime di integrazione Azure-SSIS. Questa installazione consente di usare la gestione connessione, l'origine e la destinazione ADO.NET. Prima di tutto, scaricare `ODP.NET_Managed_ODAC122cR1.zip` da [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)e quindi caricarlo insieme a `main.cmd` nel contenitore.

       7. Una cartella `SAP BW`, che contiene uno script di installazione personalizzato (`main.cmd`) per installare l'assembly SAP .NET Connector (`librfc32.dll`) in ogni nodo del runtime di integrazione Azure-SSIS Enterprise Edition (anteprima privata). Questa installazione consente di usare la gestione connessione, l'origine e la destinazione SAP BW. Prima di tutto, caricare nel contenitore la versione a 32 o 64 bit di `librfc32.dll` dalla cartella di installazione di SAP, insieme a `main.cmd`. Lo script copia quindi l'assembly SAP nella cartella `%windir%\SysWow64` o `%windir%\System32` durante l'installazione.

       8. Una cartella `STORAGE`, che contiene un'installazione personalizzata per installare Azure PowerShell in ogni nodo del runtime di integrazione Azure-SSIS. Questa installazione consente di distribuire ed eseguire pacchetti SSIS che eseguono [script di PowerShell per la gestione dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copiare `main.cmd`, un `AzurePowerShell.msi` di esempio (o installare l'ultima versione) e `storage.ps1` nel contenitore. Usare PowerShell.dtsx come modello per i pacchetti. Il modello di pacchetto combina un'[attività di download di BLOB di Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), che scarica `storage.ps1` come script di PowerShell modificabile, e un'[attività Esegui processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), che esegue lo script in ogni nodo.

       9. Una cartella `TERADATA`, che contiene uno script di installazione personalizzato (`main.cmd)` e il file associato `install.cmd`) e pacchetti di installazione (`.msi`). Questi file installano i connettori Teradata, l'API TPT e il driver ODBC in ogni nodo del runtime di integrazione Azure-SSIS Enterprise Edition (anteprima privata). Questa installazione consente di usare la gestione connessione, l'origine e la destinazione Teradata. Prima di tutto, scaricare il file ZIP Teradata Tools and Utilities (TTU) 15.x (ad esempio `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) da [Teradata](http://partnerintelligence.teradata.com) e quindi caricarlo insieme ai file `.cmd` e `.msi` precedenti nel contenitore.

    ![Cartelle nella cartella degli scenari utente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Per provare questi esempi di installazione personalizzata, copiare e incollare il contenuto dalla cartella selezionata al contenitore. Quando si riconfigura o si esegue il provisioning del runtime di integrazione Azure-SSIS con PowerShell, eseguire il cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` inserendo l'URI SAS del contenitore come valore per il nuovo parametro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Passaggi successivi

-   [Enterprise Edition del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Come sviluppare componenti personalizzati a pagamento o concessi in licenza per il runtime di integrazione Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)