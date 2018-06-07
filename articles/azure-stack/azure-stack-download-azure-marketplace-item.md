---
title: Scaricare gli elementi di marketplace da Azure | Documenti Microsoft
description: L'operatore cloud può scaricare elementi del marketplace da Azure a una distribuzione Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604533"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Scaricare gli elementi di marketplace da Azure allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come operatore di cloud, scaricare gli elementi da Azure Marketplace e renderli disponibili nello Stack di Azure. Gli elementi che è possibile scegliere provengono da un elenco di elementi di Azure Marketplace preventivamente testati e supportati per l'utilizzo di Azure Stack curato. Elementi aggiuntivi vengono spesso aggiunte a questo elenco, quindi continuare a controllare il nuovo contenuto. 

Esistono due scenari per la connessione a Azure Marketplace: 

- **Uno scenario connesso** -che richiede l'ambiente dello Stack di Azure sia connessa a internet. Utilizzare il portale di Azure Stack per individuare e scaricare gli elementi. 
- **Uno scenario disconnesso o connesso parzialmente** -che è necessario accedere a internet usando lo strumento di diffusione marketplace per scaricare elementi del marketplace. Quindi, si trasferisce il download all'installazione di Azure Stack disconnessa. Questo scenario Usa PowerShell.

Vedere [elementi di Azure Marketplace per Azure Stack](azure-stack-marketplace-azure-items.md) per un elenco degli elementi marketplace è possibile scaricare.


## <a name="connected-scenario"></a>Scenario connesso
Se Azure Stack si connette a internet, è possibile utilizzare il portale di amministrazione per il download di elementi del marketplace.

### <a name="prerequisites"></a>Prerequisiti
La distribuzione di Azure Stack deve disporre della connettività internet e da [registrato in Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Usare il portale per il download di elementi del marketplace  
1. Accedere al portale di amministrazione di Azure Stack.

2.  Esaminare lo spazio di archiviazione disponibile prima del download elementi del marketplace. In un secondo momento, quando si selezionano elementi per il download, è possibile confrontare le dimensioni di download per la capacità di archiviazione disponibile. Se la capacità è limitata, prendere in considerazione le opzioni per [la gestione dello spazio disponibile](azure-stack-manage-storage-shares.md#manage-available-space). 

    Per esaminare lo spazio disponibile, nel **la gestione delle regioni** selezionare la regione in cui si desidera esplorare e quindi passare alla **i provider di risorse** > **archiviazione**.

    ![Esaminare lo spazio di archiviazione](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Aprire Stack Azure Marketplace e connettersi ad Azure. A tale scopo, selezionare **gestione Marketplace**, quindi selezionare **Aggiungi da Azure**.

    ![Aggiungere da Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Il portale consente di visualizzare l'elenco degli elementi disponibili per il download da Azure Marketplace. È possibile fare clic su ogni elemento per visualizzarne la descrizione e informazioni aggiuntive sull'oggetto, tra cui le dimensioni di download. 

    ![Elenco di Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Selezionare l'elemento desiderato e quindi selezionare **scaricare**. Tempi di download variano.

    ![Scaricare messaggio](media/azure-stack-download-azure-marketplace-item/image04.png)

    Al termine del download, è possibile distribuire il nuovo elemento marketplace come utente o un operatore di Stack di Azure.

5. Per distribuire l'elemento scaricato, selezionare **+ nuovo**e quindi eseguire la ricerca tra le categorie per il nuovo elemento del marketplace. Successivamente, selezionare l'elemento per avviare il processo di distribuzione. Il processo varia per gli elementi del marketplace diversi. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Disconnesso o per uno scenario parzialmente connesso

Se Azure Stack in modalità disconnessa e senza la connettività internet, è possibile utilizzare PowerShell e il *dello strumento di diffusione marketplace* per scaricare gli elementi del marketplace in un computer con connettività internet. È quindi trasferire gli elementi all'ambiente dello Stack di Azure. In un ambiente disconnesso, è possibile scaricare elementi del marketplace tramite il portale di Azure Stack. 

Lo strumento di diffusione marketplace può anche essere usato in uno scenario connesso. 

Lo scenario è composto da due parti:
- **Parte 1:** scaricare da Azure Marketplace. Nel computer con accesso a internet configurare PowerShell, scaricare lo strumento di diffusione e quindi scaricare gli elementi form Azure Marketplace.  
- **Parte 2:** caricare e pubblicare nel Marketplace di Stack di Azure. Si spostare i file scaricati nell'ambiente Azure Stack, importarli in Azure Stack e quindi pubblicarli in Azure Marketplace dello Stack.  


### <a name="prerequisites"></a>Prerequisiti
- La distribuzione di Azure Stack deve essere [registrato in Azure](azure-stack-register.md).  

- Nel computer con connettività internet deve essere **modulo PowerShell dello Stack di Azure versione 1.2.11** o versione successiva. Se non è già presente, [installare i moduli di PowerShell specifici dello Stack Azure](azure-stack-powershell-install.md).  

- Per abilitare l'importazione di un elemento del marketplace scaricati, il [ambiente di PowerShell per l'operatore di Azure Stack](azure-stack-powershell-configure-admin.md) deve essere configurato.  

- È necessario disporre di un [account di archiviazione](azure-stack-manage-storage-accounts.md) nello Stack di Azure che include un contenitore accessibile pubblicamente (che è un blob di archiviazione). Utilizzare il contenitore come archivio temporaneo per i file della raccolta di elementi marketplace. Se non ha familiarità con gli account di archiviazione e i contenitori, vedere [funziona con i BLOB - portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nella documentazione di Azure.

- Lo strumento di diffusione marketplace viene scaricato durante la prima procedura. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utilizzare lo strumento di diffusione marketplace per scaricare elementi del marketplace

1. In un computer con una connessione a Internet, aprire una console di PowerShell come amministratore.

2. Aggiungere l'account di Azure che è stato utilizzato per registrare dello Stack di Azure. Per aggiungere l'account, in PowerShell eseguire `Add-AzureRmAccount` senza parametri. Viene chiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario utilizzare 2-factor authentication in base alla configurazione del tuo account.

3. Se si dispone di più sottoscrizioni, eseguire il comando seguente per selezionare il certificato utilizzato per la registrazione:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Scaricare la versione più recente dello strumento diffusione marketplace tramite lo script seguente:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importare il modulo di diffusione, quindi avviare lo strumento eseguendo lo script seguente. Sostituire i *percorso cartella di destinazione* con un percorso per archiviare i file scaricati da Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Quando si esegue lo strumento, viene chiesto di immettere le credenziali dell'account Azure. Accedi all'account di Azure che è stato utilizzato per registrare dello Stack di Azure. Dopo che l'account di accesso ha esito positivo, vedrai una schermata come nell'immagine seguente, con l'elenco di elementi del marketplace disponibili.  

   ![Popup di elementi Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Selezionare l'elemento che si desidera scaricare e annotare il *versione*. (È possibile selezionare più immagini tenendo il *Ctrl* chiave.) Si farà riferimento il *versione* quando si importa l'elemento nella procedura successiva. 
   
   È anche possibile filtrare l'elenco delle immagini tramite il **aggiungere criteri** opzione.

8. Selezionare **OK**e quindi leggere e accettare le note legali. 

9. Il tempo che accetta il download dipende dalla dimensione dell'elemento. Al termine del download, l'elemento è disponibile nella cartella specificata nello script. Il download include un file di disco rigido virtuale (per le macchine virtuali) o un oggetto. File ZIP (per estensioni di macchina virtuale). Include anche un pacchetto di raccolta nel *.azpkg* formato. (Un *.azpkg* pacchetto è un *zip* file.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importare il download e la pubblicazione in Azure Marketplace di Stack
1. I file di immagini di macchine virtuali o modelli di soluzione che presentano [scaricati in precedenza](#use-the-marketplace-syndication-tool-to-download-marketplace-items) deve essere reso disponibile in locale all'ambiente dello Stack di Azure.  

2. Importazione. File VHD allo Stack di Azure. Per importare un'immagine di macchina virtuale (VM), è necessario disporre delle informazioni seguenti sulla macchina virtuale:
   - Il *versione*, come indicato nel passaggio 7 della procedura precedente.
   - I valori per le macchine virtuali *publisher*, *offrono*, e *sku*. Per ottenere questi valori, una copia di rinominare il **.azpkg** file per modificare l'estensione di file in **zip**. È quindi possibile utilizzare un editor di testo per aprire **DeploymentTemplates\CreateUiDefinition.json**. Nel file con estensione JSON, individuare il *imageReference* sezione, che contiene questi valori per l'elemento del marketplace. Nell'esempio seguente viene illustrato come questa informazione viene visualizzata:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Importare l'immagine allo Stack di Azure utilizzando il **Add-AzsPlatformimage** cmdlet. Quando si utilizza questo cmdlet, assicurarsi di sostituire il *publisher*, *offrono*e altri valori di parametro con i valori dell'immagine che si desidera importare. È possibile ottenere il *publisher*, *offrono*, e *sku* i valori dell'immagine dal file di testo che viene scaricato insieme al file AZPKG e archiviato nel percorso di destinazione . 

   Nello script di esempio seguente, vengono utilizzati i valori per i Datacenter in Windows Server 2016 - macchina virtuale Server Core. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Informazioni sui modelli di soluzione:** alcuni modelli possono includere un piccolo 3 MB. File di disco rigido virtuale con il nome **fixed3.vhd**. Non è necessario importare il file allo Stack di Azure. Fixed3.vhd.  Questo file è incluso con alcuni modelli di soluzioni per soddisfare i requisiti di pubblicazione per Azure Marketplace.

   Esaminare la descrizione di modelli e scaricare e importare requisiti aggiuntivi, ad esempio dischi rigidi virtuali che sono necessarie per lavorare con il modello di soluzione.

3. Usare il portale di amministrazione per caricare il pacchetto di un elemento marketplace (il file .azpkg) Stack archiviazione Blob di Azure. Caricamento del pacchetto rende disponibili allo Stack di Azure in modo che in un secondo momento è possibile pubblicare l'elemento dello Stack Azure Marketplace.

   Caricamento è richiesto un account di archiviazione con un contenitore accessibile pubblicamente (vedere i prerequisiti per questo scenario)   
   1. Nel portale di amministrazione di Azure Stack, passare a **altri servizi** > **gli account di archiviazione**.  
   
   2. Selezionare un account di archiviazione dalla sottoscrizione, quindi sotto **servizio BLOB**, selezionare **contenitori**.  
      ![Servizio BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Selezionare il contenitore in cui si desidera utilizzare e quindi selezionare **caricare** per aprire la **caricamento blob** riquadro.  
      ![Contenitore](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. Nel riquadro di blob di caricamento, individuare i file che si desidera caricare nell'account di archiviazione e quindi scegliere **caricare**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. File caricati vengono visualizzati nel riquadro del contenitore. Selezionare un file e quindi copiare l'URL dal **delle proprietà del Blob** riquadro. Si userà questo URL nel passaggio successivo, quando si importa l'elemento del marketplace allo Stack di Azure.  Nella figura seguente, il contenitore è *archiviazione blob-test* e il file è *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Il file URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Proprietà BLOB](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Utilizzare PowerShell per pubblicare l'elemento del marketplace allo Stack di Azure tramite il **Add-AzsGalleryItem** cmdlet. Ad esempio:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Dopo aver pubblicato un elemento della raccolta, è possibile visualizzarlo dal **altri servizi** > **Marketplace**.  Se il download è un modello di soluzione, assicurarsi di che aggiungere un'immagine di disco rigido virtuale dipendenti per il modello di soluzione.  
  ![Marketplace di visualizzazione](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Con la versione di Azure PowerShell Stack 1.3.0 è ora possibile aggiungere estensioni di macchine virtuali.

Ad esempio: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Passaggi successivi
[Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)