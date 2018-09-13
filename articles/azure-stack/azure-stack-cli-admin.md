---
title: Abilitare la CLI di Azure per gli utenti di Azure Stack | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire le risorse in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: mabrigg
ms.openlocfilehash: 09c551ea7196ae20a60a5dd34c1cda889ff5df46
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35651725"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Abilitare la CLI di Azure per gli utenti di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile fornire il certificato radice CA agli utenti di Azure Stack, in modo da poter usare Azure CLI nei computer di sviluppo. Gli utenti saranno necessario il certificato per gestire le risorse tramite CLI.

* **Il certificato radice CA Azure Stack** è obbligatorio se gli utenti usano della riga di comando da una workstation all'esterno di Azure Stack Development Kit.  

* **L'endpoint di macchina virtuale gli alias** fornisce un alias, ad esempio "UbuntuLTS" o "Win2012Datacenter", che fa riferimento a un editore di immagini, offerta, SKU e versione come parametro singolo quando si distribuiscono le macchine virtuali.  

Le sezioni seguenti descrivono come ottenere questi valori.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Esportare il certificato di autorità di certificazione di Azure Stack radice

È possibile trovare il certificato radice CA Azure Stack nel kit di sviluppo e in una macchina virtuale tenant in cui è in esecuzione all'interno dell'ambiente kit di sviluppo. Per esportare il certificato radice di Azure Stack in formato PEM, accedere al kit di sviluppo o la macchina virtuale tenant ed eseguire lo script seguente:

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

Operatori di Azure Stack devono essere configurata da un endpoint accessibile pubblicamente che ospita un file di alias di macchina virtuale. Il file di alias di macchina virtuale è un file JSON che fornisce un nome comune per un'immagine. Tale nome viene successivamente specificato quando una macchina virtuale viene distribuita come parametro di comando di Azure.  

Prima di aggiungere una voce in un file alias, assicurarsi che si [scaricare immagini da Azure Marketplace](azure-stack-download-azure-marketplace-item.md), o avere [pubblicata un'immagine personalizzata](azure-stack-add-vm-image.md). Se si pubblica un'immagine personalizzata, prendere nota delle informazioni server di pubblicazione, offerta, SKU e versione specificati durante la pubblicazione. Se è un'immagine del marketplace, è possibile visualizzare le informazioni usando il ```Get-AzureVMImage``` cmdlet.  

Oggetto [file di esempio alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con molte immagini comuni alias è disponibile. È possibile utilizzarlo come punto di partenza. Ospitare questo file in uno spazio in cui i client della riga di comando possono raggiungerlo. Uno consiste nell'ospitare i file in un account di archiviazione blob e condividerne l'URL con gli utenti:

1. Scaricare il [file di esempio](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) da GitHub.
2. Creare un nuovo account di archiviazione in Azure Stack. Al termine, creare un nuovo contenitore blob. Impostare i criteri di accesso per "pubblico".  
3. Caricare il file JSON nel nuovo contenitore. Al termine, è possibile visualizzare l'URL del blob selezionando il nome del blob e quindi selezionando l'URL dalle proprietà del blob.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)

- [Connettersi con PowerShell](azure-stack-connect-powershell.md)

- [Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
