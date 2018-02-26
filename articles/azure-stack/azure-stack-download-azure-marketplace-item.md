---
title: Scaricare gli elementi di marketplace da Azure | Documenti Microsoft
description: "È possibile scaricare gli elementi di marketplace da Azure a una distribuzione Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 27b575a1baa793794480d16e91f0f96355b3d303
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Scaricare gli elementi di marketplace da Azure allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*


Come si decide il contenuto da includere nel marketplace dello Stack di Azure, è necessario considerare il contenuto disponibile da Azure marketplace. È possibile scaricare da un elenco di elementi di Azure marketplace che sono stati pre-verificate per l'esecuzione in Azure Stack curato. Spesso vengono aggiunti nuovi elementi a questo elenco, pertanto assicurarsi di selezionare nuovamente per il nuovo contenuto.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Scaricare elementi del marketplace in uno scenario connesso (con connettività internet)

1. Per scaricare elementi del marketplace, è innanzitutto necessario [registro dello Stack di Azure con Azure](azure-stack-register.md).
2. Accedere al portale di amministrazione di Azure Stack (https://portal.local.azurestack.external).
3. Alcuni elementi del marketplace possono essere estesa. Verificare che si dispone di spazio sufficiente nel sistema, fare clic su **i provider di risorse** > **archiviazione**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Fare clic su **più servizi** > **gestione Marketplace**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Fare clic su **Aggiungi da Azure** per visualizzare un elenco di elementi disponibili per il download. È possibile fare clic su ogni elemento nell'elenco per visualizzarne la descrizione e dimensioni del download.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Selezionare l'elemento desiderato nell'elenco e quindi fare clic su **scaricare**. Immagine di macchina virtuale per l'elemento selezionato viene avviato per il download. Tempi di download variano.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Al termine del download, è possibile distribuire il nuovo elemento marketplace come utente o un operatore di Stack di Azure. Fare clic su **+ nuovo**, eseguire la ricerca tra le categorie per il nuovo elemento marketplace e quindi selezionare l'elemento.
7. Fare clic su **crea** per aprire l'esperienza di creazione per l'elemento appena scaricato. Seguire le istruzioni dettagliate per distribuire l'elemento.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Download di elementi del marketplace in un disconnesso o per uno scenario di connesso parziale (con connettività internet limitata)

Quando si distribuisce Azure Stack in modalità disconnessa (senza qualsiasi connessione a internet), è possibile scaricare elementi del marketplace tramite il portale di Azure Stack. Tuttavia, è possibile utilizzare lo strumento di diffusione marketplace per scaricare gli elementi del marketplace in un computer con connettività internet e quindi di trasferirle all'ambiente dello Stack di Azure.

### <a name="prerequisites"></a>Prerequisiti
Prima di utilizzare lo strumento di diffusione marketplace, assicurarsi di aver [registrati dello Stack di Azure con la sottoscrizione di Azure](azure-stack-register.md).  

Dal computer con connettività internet, utilizzare la procedura seguente per scaricare gli elementi necessari marketplace:

1. Aprire la console PowerShell come amministratore e [installare i moduli di PowerShell specifici di Azure Stack](azure-stack-powershell-install.md). Assicurarsi di installare **PowerShell versione 1.2.11 o versione successiva**.  

2. Aggiungere l'account di Azure che è stato utilizzato per registrare dello Stack di Azure. Per aggiungere l'account, eseguire il **Aggiungi AzureRmAccount** cmdlet senza parametri. Viene richiesto di immettere le credenziali dell'account Azure e potrebbe essere necessario utilizzare 2-factor authentication in base alla configurazione dell'account.  

3. Se si dispone di più sottoscrizioni, eseguire il comando seguente per selezionare il certificato utilizzato per la registrazione:  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Scaricare la versione più recente dello strumento di diffusione marketplace tramite lo script seguente:  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-master

   ```

5. Importare il modulo di diffusione e avviare lo strumento di esecuzione dei comandi seguenti:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Quando si esegue lo strumento, viene chiesto di immettere le credenziali dell'account Azure. Accedi all'account di Azure che è stato utilizzato per registrare dello Stack di Azure. Al termine dell'account di accesso ha esito positivo, visualizzata la schermata seguente con l'elenco di elementi del marketplace disponibili.  

   ![Popup di elementi Azure Marketplace](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Selezionare l'immagine che si desidera scaricare e prendere nota della versione dell'immagine. È possibile selezionare più immagini tenendo premuto il tasto Ctrl. Utilizzare la versione dell'immagine per importare l'immagine nella sezione successiva.  Successivamente, fare clic su **Ok**, quindi accettare i termini legali facendo clic su **Sì**. È anche possibile filtrare l'elenco delle immagini tramite il **aggiungere criteri** opzione. 

   Il download richiede un certo tempo a seconda delle dimensioni dell'immagine. Una volta il download delle immagini è disponibile nel percorso di destinazione fornito in precedenza. Il download contiene gli elementi di raccolta e i file VHD nel formato Azpkg.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importare l'immagine e pubblicarla in marketplace Azure Stack

1. Dopo aver scaricato il pacchetto di raccolta e di immagine, salvare loro e il relativo contenuto nella cartella AzureStack-strumenti-master per un'unità disco rimovibile e copiarlo in un ambiente dello Stack di Azure (è possibile copiarlo in locale in qualsiasi posizione, ad esempio: "C:\MarketplaceImages").   

2. Prima di importare l'immagine, è necessario connettersi all'ambiente dell'operatore dello Stack di Azure tramite la procedura descritta in [configurare l'ambiente di PowerShell Azure Stack operatore](azure-stack-powershell-configure-admin.md).  

3. Importare l'immagine dello Stack di Azure tramite il cmdlet Add-AzsVMImage. Quando si utilizza questo cmdlet, assicurarsi di sostituire il *publisher*, *offrono*e altri valori di parametro con i valori dell'immagine che si desidera importare. È possibile ottenere il *publisher*, *offrono*, e *sku* i valori dell'immagine dall'oggetto imageReference del file Azpkg che è stato scaricato in precedenza e  *versione* valore al passaggio 6 nella sezione precedente.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Sostituire i valori dei parametri ed eseguire il comando seguente:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Portale di utilizzo per caricare l'elemento del Marketplace (. Azpkg) Stack archiviazione Blob di Azure. È possibile caricare in archiviazione di Azure Stack locale o caricare in archiviazione di Azure. (È un percorso temporaneo per il pacchetto). Assicurarsi che il blob è accessibile pubblicamente e prendere nota dell'URI.  

5. Pubblicare l'elemento del marketplace allo Stack di Azure tramite il **Aggiungi AzsGalleryItem**. Ad esempio: 

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. Dopo l'elemento della raccolta viene pubblicato, è possibile visualizzarlo dal **New** > **Marketplace** riquadro.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Passaggi successivi

[Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)
