---
title: Abilitare la CLI di Azure per gli utenti di Azure Stack | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire le risorse in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 01/15/2019
ms.author: mabrigg
ms.lastreviewed: 01/15/2019
ms.openlocfilehash: 34a33c82813a0059cc286a7cb2d1fe984a5bf6d2
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452404"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Abilitare la CLI di Azure per gli utenti di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile fornire il certificato radice CA agli utenti di Azure Stack, in modo da poter usare Azure CLI nei computer di sviluppo. Gli utenti devono il certificato per gestire le risorse tramite CLI.

 - **Il certificato radice CA Azure Stack** è obbligatorio se gli utenti usano della riga di comando da una workstation all'esterno di Azure Stack Development Kit.  

 - **L'endpoint di macchina virtuale gli alias** fornisce un alias, ad esempio "UbuntuLTS" o "Win2012Datacenter", che fa riferimento a un editore di immagini, offerta, SKU e versione come parametro singolo quando si distribuiscono le macchine virtuali.  

Le sezioni seguenti descrivono come ottenere questi valori.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Esportare il certificato di autorità di certificazione di Azure Stack radice

Se si usa un sistema integrato, non è necessario esportare il certificato di autorità di certificazione radice. È necessario esportare il certificato radice CA in un Azure Stack Development Kit (ASDK).

Per esportare il certificato radice ASDK nel formato PEM, accedere ed eseguire lo script seguente:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurare l'endpoint di alias di macchina virtuale

Operatori di Azure Stack devono essere configurata da un endpoint accessibile pubblicamente che ospita un file di alias di macchina virtuale. Il file di alias di macchina virtuale è un file JSON che fornisce un nome comune per un'immagine. Si userà il nome quando si distribuisce una macchina virtuale come parametro di comando di Azure.  

Prima di aggiungere una voce in un file alias, assicurarsi che si [scaricare immagini da Azure Marketplace](azure-stack-download-azure-marketplace-item.md) o avere [pubblicata un'immagine personalizzata](azure-stack-add-vm-image.md). Se si pubblica un'immagine personalizzata, prendere nota delle informazioni server di pubblicazione, offerta, SKU e versione specificati durante la pubblicazione. Se è un'immagine del marketplace, è possibile visualizzare le informazioni usando il ```Get-AzureVMImage``` cmdlet.  

Oggetto [file di esempio alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con molte immagini comuni alias è disponibile. È possibile utilizzarlo come punto di partenza. Ospitare questo file in uno spazio in cui i client della riga di comando possono raggiungerlo. Uno consiste nell'ospitare i file in un account di archiviazione blob e condividerne l'URL con gli utenti:

1. Scaricare il [file di esempio](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) da GitHub.
2. Creare un account di archiviazione in Azure Stack. Al termine, creare un contenitore blob. Impostare i criteri di accesso per "pubblico".  
3. Caricare il file JSON nel nuovo contenitore. Al termine, è possibile visualizzare l'URL del blob. Selezionare il nome del blob e quindi selezionando l'URL dalle proprietà del blob.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
- [Connettersi con PowerShell](azure-stack-connect-powershell.md)
- [Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
