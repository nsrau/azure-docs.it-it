---
title: Abilitare l'interfaccia CLI di Azure per gli utenti di Azure Stack | Documenti Microsoft
description: Informazioni su come utilizzare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire le risorse sullo Stack di Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: e2483bda5a0c6a6b270759946f146c37c5dad5b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Abilitare l'interfaccia CLI di Azure per gli utenti di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Non ci siano operazioni operatore specifico dello Stack di Azure che è possibile eseguire tramite l'interfaccia CLI di Azure. Ma prima che gli utenti possono gestire le risorse tramite CLI, gli operatori di Azure Stack devono fornire loro le operazioni seguenti:

* **Il certificato radice CA Azure Stack** è obbligatorio se gli utenti usano CLI da una workstation all'esterno di Azure Stack Development Kit.  

* **L'endpoint della macchina virtuale alias** fornisce un alias, ad esempio "UbuntuLTS" o "Win2012Datacenter", che fa riferimento a un server di pubblicazione di immagine, offerta, SKU e versione come un singolo parametro durante la distribuzione di macchine virtuali.  

Le sezioni seguenti descrivono come ottenere questi valori.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Esportare il certificato radice CA Azure Stack

Il certificato radice CA dello Stack di Azure è disponibile nel kit di sviluppo e in una macchina virtuale tenant che è in esecuzione all'interno dell'ambiente di kit di sviluppo. Per esportare il certificato radice dello Stack di Azure nel formato PEM, accedi al kit di sviluppo o la macchina virtuale tenant ed eseguire lo script seguente:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurare l'endpoint di alias di macchina virtuale

Gli operatori di Azure Stack devono configurare un endpoint accessibile pubblicamente che ospita un file di alias di macchina virtuale. Il file di alias di macchina virtuale è un file JSON che fornisce un nome comune per un'immagine. Tale nome viene successivamente specificato quando una macchina virtuale viene distribuita come parametro di interfaccia CLI di Azure.  

Prima di aggiungere una voce in un file di alias, assicurarsi che si [scaricamento delle immagini da Azure Marketplace](azure-stack-download-azure-marketplace-item.md), o [pubblicato un'immagine personalizzata](azure-stack-add-vm-image.md). Se si pubblica un'immagine personalizzata, prendere nota delle informazioni server di pubblicazione, offerta, SKU e versione specificati durante la pubblicazione. In caso di un'immagine del marketplace, è possibile visualizzare le informazioni utilizzando il ```Get-AzureVMImage``` cmdlet.  
   
Oggetto [file di esempio alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con molti immagine comune alias è disponibile. È possibile utilizzarlo come punto di partenza. Ospitare il file in uno spazio in cui è possono utilizzare i client CLI. Un modo consiste nell'ospitare il file in un account di archiviazione blob e condividere l'URL con gli utenti:

1. Scaricare il [file di esempio](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) da GitHub.
2. Creare un nuovo account di archiviazione nello Stack di Azure. Creare un nuovo contenitore blob quando che è stata completata. Impostare i criteri di accesso per "pubblica".  
3. Caricare il file JSON per il nuovo contenitore. Quando è completa, è possibile visualizzare l'URL del blob selezionando il nome di blob e quindi selezionando l'URL dalle proprietà del blob.


## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)

[Connettersi con PowerShell](azure-stack-connect-powershell.md)

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)

