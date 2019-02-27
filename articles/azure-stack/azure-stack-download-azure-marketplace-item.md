---
title: Scaricare elementi di marketplace di Azure | Microsoft Docs
description: L'operatore cloud può scaricare elementi di marketplace di Azure per la distribuzione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: c3c577ce2483c5de3aa458c7fe4b6fced418f684
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882944"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Scaricare elementi di marketplace di Azure ad Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Come operatore di cloud, è possibile scaricare gli elementi da Azure Marketplace e renderle disponibili in Azure Stack. Gli elementi che è possibile scegliere provengono da un elenco curato di elementi del Marketplace di Azure che vengono pre-testato e supportato per l'uso con Azure Stack. Elementi aggiuntivi vengono spesso aggiunte a questo elenco, quindi continuare a ricontrollare in seguito per il nuovo contenuto. 

Esistono due scenari per la connessione ad Azure Marketplace: 

- **Uno scenario connesso** -che richiede l'ambiente Azure Stack per essere connesso a internet. Usare il portale di Azure Stack per individuare e scaricare gli elementi. 
- **Uno scenario parzialmente connesso o disconnesso** -che richiede l'accesso a Internet utilizzando lo strumento di diffusione di marketplace per scaricare elementi di marketplace. Quindi, visualizzare i download disponibili è trasferire all'installazione di Azure Stack disconnesso. Questo scenario Usa PowerShell.

Visualizzare [elementi di Azure Marketplace per Azure Stack](azure-stack-marketplace-azure-items.md) per un elenco degli elementi di marketplace, è possibile scaricare.


## <a name="connected-scenario"></a>Scenario connesso
Se Azure Stack si connette a internet, è possibile utilizzare il portale di amministrazione per scaricare elementi di marketplace.

### <a name="prerequisites"></a>Prerequisiti
Distribuzione di Azure Stack deve disporre della connettività internet e deve essere [registrata con Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Usare il portale per scaricare elementi di marketplace  
1. Accedere al portale di amministrazione di Azure Stack.

2.  Esaminare lo spazio di archiviazione disponibile prima del download di elementi del marketplace. In un secondo momento, quando si selezionano elementi per il download, è possibile confrontare le dimensioni del download per la capacità di archiviazione disponibile. Se la capacità è limitata, prendere in considerazione le opzioni per [la gestione dello spazio disponibile](azure-stack-manage-storage-shares.md#manage-available-space). 

    Per esaminare lo spazio disponibile, nel **gestione delle aree** selezionare l'area in cui si desidera esplorare e quindi passare alla **provider di risorse** > **archiviazione**.

    [![Esaminare lo spazio di archiviazione](media/azure-stack-download-azure-marketplace-item/storagesm.png "esaminare lo spazio di archiviazione")](media/azure-stack-download-azure-marketplace-item/storage.png#lightbox)

    
3. Aprire il Marketplace di Azure Stack e connettersi ad Azure. A tale scopo, selezionare **gestione Marketplace**, quindi selezionare **Add da Azure**.

    [![Aggiunta di Azure](media/azure-stack-download-azure-marketplace-item/marketplacesm.png "aggiungere da Azure")](media/azure-stack-download-azure-marketplace-item/marketplace.png#lightbox)

    Il portale Visualizza l'elenco di elementi disponibili per il download da Azure Marketplace. È possibile fare clic su ogni elemento per visualizzare la descrizione e informazioni aggiuntive, tra cui le dimensioni di download. 

    [![Marketplace elencati](media/azure-stack-download-azure-marketplace-item/image03sm.png "Marketplace elencati")](media/azure-stack-download-azure-marketplace-item/image03.png#lightbox)

4. Selezionare l'elemento desiderato e quindi selezionare **scaricare**. Tempi di download variare.

    [![Messaggio di download](media/azure-stack-download-azure-marketplace-item/image04.png "relativo al Download")](media/azure-stack-download-azure-marketplace-item/image04.png#lightbox)

    Al termine del download, è possibile distribuire il nuovo elemento del marketplace come operatore di Azure Stack o un'utente.

5. Per distribuire l'elemento scaricato, selezionare **+ crea una risorsa**e quindi eseguire una ricerca tra le categorie per il nuovo elemento del marketplace. Selezionare quindi l'elemento per avviare il processo di distribuzione. Il processo varia per gli elementi del marketplace diversi. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Disconnesso o uno scenario di connesso parziale

Se Azure Stack in modalità senza connessione e senza connettività internet, è possibile utilizzare PowerShell e il *dello strumento di diffusione di marketplace* per scaricare gli elementi del marketplace in un computer con connettività internet. È quindi possibile trasferire gli elementi nell'ambiente Azure Stack. In un ambiente disconnesso, è possibile scaricare elementi del marketplace tramite il portale di Azure Stack. 

Lo strumento di diffusione marketplace è anche utilizzabile in uno scenario connesso. 

Lo scenario è composto da due parti:
- **Parte 1:** Scaricare da Azure Marketplace. Nel computer con accesso a internet configurare PowerShell, scaricare lo strumento di diffusione e quindi scaricare form degli elementi di Azure Marketplace.  
- **Parte 2:** Caricare e pubblicare nel Marketplace di Azure Stack. Spostare i file scaricati all'ambiente Azure Stack, importarli in Azure Stack e quindi pubblicarli in Azure Stack Marketplace.  


### <a name="prerequisites"></a>Prerequisiti
- Distribuzione di Azure Stack deve essere [registrata con Azure](azure-stack-register.md).  

- Nel computer che abbia la connettività internet deve essere **modulo di PowerShell di Azure Stack versione 1.2.11** o versione successiva. Se non è già presente, [installa i moduli di PowerShell specifici di Azure Stack](azure-stack-powershell-install.md).  

- Per abilitare l'importazione di un elemento del marketplace scaricato, il [ambiente di PowerShell per l'operatore di Azure Stack](azure-stack-powershell-configure-admin.md) deve essere configurato.  

- È necessario disporre di un [account di archiviazione](azure-stack-manage-storage-accounts.md) nello Stack di Azure che include un contenitore accessibile pubblicamente (ovvero un blob di archiviazione). Usare il contenitore come risorsa di archiviazione temporanea per i file di raccolta di elementi di marketplace. Se non ha familiarità con gli account di archiviazione e i contenitori, vedere [usare i BLOB - portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nella documentazione di Azure.

- Lo strumento di diffusione marketplace viene scaricato durante la prima procedura. 

- È possibile installare [AzCopy](../storage/common/storage-use-azcopy.md) per download ottimale delle prestazioni, ma questo non è obbligatoria.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Usare lo strumento di diffusione di marketplace per scaricare elementi di marketplace

1. In un computer con connessione Internet, aprire una console PowerShell come amministratore.

2. Aggiungere l'account di Azure che è stata utilizzata per registrare Azure Stack. Per aggiungere l'account, in PowerShell eseguire `Add-AzureRmAccount` senza parametri. Viene chiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario usare l'autenticazione a 2 fattori in base alla configurazione dell'account.

3. Se si hanno più sottoscrizioni, eseguire il comando seguente per selezionare il certificato che è stato usato per la registrazione:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Scaricare la versione più recente dello strumento di diffusione di marketplace con lo script seguente:  

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

5. Importare il modulo di diffusione, quindi avviare lo strumento eseguendo i comandi seguenti. Sostituire `Destination folder path` con un percorso per archiviare i file scaricati da Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

6. Quando si esegue lo strumento, si verrà visualizzata una schermata simile all'immagine seguente, con l'elenco degli elementi disponibili nel marketplace:

   [![Controllo popup degli elementi di Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "elementi del Marketplace di Azure")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Selezionare l'elemento che si desidera scaricare e annotare la *versione*. È possibile tenere premuto il *Ctrl* chiave per selezionare più immagini. Si fa riferimento il *versione* quando si importa l'elemento nella procedura successiva. 
   
   È anche possibile filtrare l'elenco delle immagini usando il **Aggiungi criteri** opzione.

8. Selezionare **OK**e quindi leggere e accettare le condizioni legali. 

9. Il tempo impiegato per il download dipende dalle dimensioni dell'elemento. Al termine del download, l'elemento è disponibile nella cartella specificata nello script. Il download comprende un file di disco rigido virtuale (per le macchine virtuali) o un file ZIP (per estensioni di macchina virtuale). Può anche includere un pacchetto di raccolta nel *azpkg* formato, che è semplicemente un file con estensione zip.

10. Se il download ha esito negativo, è possibile ripetere l'operazione eseguendo di nuovo il cmdlet di PowerShell seguente:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes”
    ```

    Prima di riprovare, rimuovere la cartella di prodotto in cui il download non riuscito. Ad esempio, se lo script di download ha esito negativo quando si scaricano `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, rimuovere il `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` cartella, quindi eseguire nuovamente il cmdlet.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Importare il download e la pubblicazione in Marketplace Azure Stack (1811 e versioni successive)

1. È necessario spostare i file che hai [scaricati in precedenza](#use-the-marketplace-syndication-tool-to-download-marketplace-items) localmente in modo che siano disponibili nell'ambiente Azure Stack. Lo strumento di diffusione marketplace deve essere disponibile nell'ambiente Azure Stack, anche perché è necessario usare lo strumento per eseguire l'operazione di importazione.

   L'immagine seguente mostra un esempio di struttura di cartelle. `D:\downloadfolder` contiene tutti gli elementi di marketplace scaricato. Ogni sottocartella è un elemento del marketplace (ad esempio, `microsoft.custom-script-linux-arm-2.0.3`), denominato per l'ID prodotto. All'interno di ogni sottocartella è contenuto scaricato dell'elemento del marketplace.

   [![Struttura di directory di download di Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "struttura di directory di download di Marketplace")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Seguire le istruzioni in [questo articolo](azure-stack-powershell-configure-admin.md) per configurare la sessione di PowerShell per Azure Stack operatore. 

3. Importare il modulo di diffusione e quindi avviare lo strumento di diffusione marketplace eseguendo lo script seguente:

   ```PowerShell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Il `-origin` parametro specifica la cartella di primo livello che contiene tutti i prodotti scaricati; ad esempio, `"D:\downloadfolder"`.

   `-AzsCredential` è facoltativo. Si è usato per rinnovare il token di accesso, se è scaduto. Se il `-AzsCredential` parametro viene omesso e il token scade, viene richiesto di immettere le credenziali di operatore.

    > [!Note]  
    > ADFS supporta solo l'autenticazione interattiva con le identità degli utenti. Se è necessario un oggetto credenziale è necessario usare un'entità servizio (SPN). Per altre informazioni sulla configurazione di un'entità servizio con Azure Stack e AD FS come servizio di gestione di identità, vedere [Gestisci entità servizio per AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Dopo che lo script viene completato correttamente, l'elemento deve essere disponibile in Azure Stack Marketplace.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Importare il download e la pubblicazione in Marketplace Azure Stack (1809 e versioni precedenti)

1. I file di immagini di macchine virtuali o modelli di soluzione che hai [scaricati in precedenza](#use-the-marketplace-syndication-tool-to-download-marketplace-items) devono essere rese disponibili in locale nell'ambiente Azure Stack.  

2. Usare il portale di amministrazione per caricare il pacchetto di elemento di marketplace (il file con estensione azpkg) e l'immagine di disco rigido virtuale (file con estensione vhd) in archiviazione Blob di Azure Stack. Caricamento del pacchetto e i file del disco li rende disponibili in Azure Stack in modo che è possibile pubblicare l'elemento in un secondo momento per il Marketplace di Azure Stack.

   Caricamento richiede di disporre di un account di archiviazione con un contenitore accessibile pubblicamente (vedere i prerequisiti per questo scenario).  
   1. Nel portale di amministrazione di Azure Stack, passare a **tutti i servizi** e quindi nel **dati e archiviazione** categoria, seleziona **account di archiviazione**.  
   
   2. Selezionare un account di archiviazione dalla sottoscrizione e quindi sotto **servizio BLOB**, selezionare **contenitori**.  
      [![Servizio BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png "servizio Blob")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Selezionare il contenitore a cui si vuole usare e quindi selezionare **caricare** per aprire il **carica blob** riquadro.  
      [![Container](media/azure-stack-download-azure-marketplace-item/container.png "Container")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. Nel riquadro blob Upload, individuare i file di pacchetto e il disco da caricare nell'archivio e quindi selezionare **caricare**: [![Upload](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Upload")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. File caricati vengono visualizzati nel riquadro del contenitore. Selezionare un file e quindi copiare l'URL dal **proprietà Blob** riquadro. Si userà questo URL nel passaggio successivo quando si importa l'elemento del marketplace per Azure Stack.  Nell'immagine seguente, il contenitore è *test-blob-storage* e il file sia *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Il file è di URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      [![Proprietà BLOB](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "proprietà Blob")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importare l'immagine di disco rigido virtuale di Azure Stack usando il **Add-AzsPlatformimage** cmdlet. Quando si utilizza questo cmdlet, sostituire il *server di pubblicazione*, *offrono*e altri valori di parametro con i valori dell'immagine che si desidera importare. 

   È possibile ottenere il *server di pubblicazione*, *offrono*, e *sku* i valori dell'immagine dal file di testo che consente di scaricare il file con estensione AZPKG. Il file di testo viene archiviato nel percorso di destinazione. Il *versione* valore è la versione osservata durante il download dell'elemento da Azure nella procedura precedente. 
 
   Nello script di esempio seguente, vengono usati i valori per Windows Server 2016 Datacenter - macchina virtuale di base del Server. Il valore per *- Osuri* è un percorso di esempio per la posizione di archiviazione blob per l'elemento.

   Come alternativa a questo script, è possibile usare la [descritta in questo articolo](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) per importare il. Immagine di disco rigido virtuale nel portale di Azure.

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Informazioni sui modelli di soluzione:** Alcuni modelli possono includere un piccolo 3 MB. File di disco rigido virtuale con il nome **fixed3.vhd**. Non è necessario importare il file di Azure Stack. Fixed3.vhd.  Questo file è incluso con alcuni modelli di soluzione per soddisfare i requisiti di pubblicazione per Azure Marketplace.

   Esaminare la descrizione di modelli e scaricare e importare requisiti aggiuntivi, ad esempio dischi rigidi virtuali che sono necessari per lavorare con il modello di soluzione.  
   
   **Informazioni sulle estensioni:** Quando si lavora con le estensioni di immagine di macchina virtuale, usare i parametri seguenti:
   - *Autore*
   - *Tipo*
   - *Versione*  

   Non si usa *offrono* per le estensioni.   


4.  Usare PowerShell per pubblicare l'elemento del marketplace in Azure Stack usando il **Add-AzsGalleryItem** cmdlet. Ad esempio:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Dopo aver pubblicato un elemento della raccolta, è ora disponibile per l'uso. Per verificare che l'elemento della raccolta viene pubblicato, passare a **tutti i servizi**, quindi nel **generali** categoria, seleziona **Marketplace**.  Se il download è un modello di soluzione, assicurarsi che si aggiunge un'immagine di disco rigido virtuale dipendenti per tale modello di soluzione.  
  [![Marketplace di visualizzazione](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "marketplace di visualizzazione")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Con la versione di PowerShell per Azure Stack 1.3.0 è ora possibile aggiungere estensioni della macchina virtuale. Ad esempio: 

```PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Passaggi successivi

[Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)