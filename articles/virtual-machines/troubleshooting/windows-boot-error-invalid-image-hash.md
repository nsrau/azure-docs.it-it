---
title: Errore di Windows Boot Manager-0xC0000428 stato dell'hash dell'immagine non valido
titlesuffix: Azure Virtual Machines
description: Questo articolo illustra la procedura per risolvere i problemi relativi all'uso di un'immagine di anteprima e alla scadenza del periodo di valutazione, che impedisce l'avvio di una macchina virtuale (VM) di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: e58e349d7b7385ec913986c39462c17deadcb61d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969603"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Errore di Windows Boot Manager-0xC0000428 stato dell'hash dell'immagine non valido

Questo articolo illustra la procedura per risolvere i problemi relativi all'uso di un'immagine di anteprima e alla scadenza del periodo di valutazione, che impedisce l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che nella schermata viene visualizzato Windows Boot Manager con il messaggio:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![Nella figura 1 viene visualizzato Windows Boot Manager con lo stato "Ox0000428" e informazioni "Windows non è in grado di verificare la firma digitale per il file".](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

oppure il messaggio:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![Nella figura 2 viene visualizzato Windows Boot Manager con lo stato "Ox0000428" e informazioni "non è stato possibile verificare la firma digitale per questo file".](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Causa

L'immagine usata per compilare la macchina virtuale è un'immagine di anteprima con una data di scadenza anziché un'immagine RTM (Release to Manufacturing). 

Le immagini di anteprima hanno un ciclo di vita designato e lo screenshot visualizzato viene visualizzato quando si passa la data di scadenza, ovvero la versione di valutazione dell'immagine è finita.

### <a name="example-of-preview-images"></a>Esempio di immagini di anteprima

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Non è possibile estendere la data di scadenza di un'immagine di anteprima. Una volta scaduta la versione di anteprima, la macchina virtuale non sarà più in grado di eseguire l'avvio.

- A seconda del prodotto, il periodo di valutazione può variare. Le immagini di anteprima di Windows, ad esempio, hanno un periodo di prova di 180 giorni.

- In Azure tutte le immagini per Windows con versioni di anteprima includeranno una nota nella relativa descrizione che non sono destinate alla produzione e che sono disponibili per l'uso solo per un periodo di valutazione specificato o come "versione di anteprima".

## <a name="solution"></a>Soluzione

Se l'immagine è un'immagine di anteprima, non è possibile estendere la data di scadenza per l'immagine usata, quindi è necessario [distribuire una nuova macchina virtuale](../windows/quick-create-portal.md) usando un'immagine non di anteprima. La procedura seguente consente di identificare se è stata usata un'immagine di anteprima, oltre a fornire le risorse necessarie per il trasferimento dei dati da questa macchina virtuale a una nuova VM. Se l'immagine è stata identificata in modo positivo come immagine di anteprima, l'immagine non è recuperabile, perché ora è scaduta.

A seconda delle preferenze, è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per eseguire query sull'immagine per determinare se si tratta di un'immagine di anteprima. È possibile usare questi comandi per confermare che l'immagine è un'immagine di anteprima.

### <a name="query-using-azure-powershell"></a>Query con Azure PowerShell

1. Aprire l'applicazione Windows PowerShell.
1. Eseguire i comandi seguenti:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- Nei comandi precedenti sostituire `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` e `<YEAR WHEN THIS IMAGE WAS RELEASED>` con le informazioni indicate. Rimuovere anche i simboli "<" e ">".

  Vedere l'esempio seguente:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Eseguire query con l'interfaccia della riga di comando di Azure

1. Se non è già stato fatto, sarà necessario [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure.
1. Al termine del download, usare il prompt dei comandi o PowerShell per immettere il `az login` comando e quindi accedere con le credenziali dell'account.
1. Una volta effettuato l'accesso, immettere i comandi seguenti:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- Nei comandi precedenti sostituire `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` e `<YEAR WHEN THIS IMAGE WAS RELEASED>` con le informazioni indicate. Rimuovere anche i simboli "<" e ">".

  Vedere l'esempio seguente:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```