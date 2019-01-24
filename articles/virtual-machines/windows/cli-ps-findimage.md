---
title: Selezionare immagini di VM Windows in Azure | Microsoft Docs
description: Usare Azure PowerShell per determinare l'editore, l'offerta, lo SKU e la versione per le immagini di macchine virtuali del Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: danlep
ms.openlocfilehash: ff4ccdf28be9d28798fff0e9f66bbb2c860166b7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424541"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Trovare immagini di macchine virtuali Windows in Azure Marketplace con Azure PowerShell

Questo articolo descrive come usare Azure PowerShell per trovare immagini di VM in Microsoft Azure Marketplace. È possibile specificare un'immagine del Marketplace quando si crea una macchina virtuale a livello di codice mediante PowerShell, modelli di Resource Manager o altri strumenti.

È anche possibile sfogliare le immagini e le offerte disponibili usando la vetrina di [Azure Marketplace](https://azuremarketplace.microsoft.com/), il [portale di Azure](https://portal.azure.com) o l'[interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md). 

Verificare di avere installato e configurato il [modulo di Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) più recente.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabella delle immagini Windows usate comunemente
| Editore | Offerta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2017-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Esplorare le immagini

Un altro modo per trovare un'immagine in una posizione consiste nell'eseguire in sequenza i cmdlet [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) e [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku):

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.

Quindi, per uno SKU selezionato, eseguire [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) per ottenere un elenco delle versioni da distribuire.

1. Elencare gli editori:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Specificare il nome dell'editore scelto ed elencare le offerte:

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Specificare il nome dell'offerta scelta ed elencare gli SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```
    
4. Specificare il nome dello SKU scelto e ottenere la versione dell'immagine:

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Dall'output del comando `Get-AzureRMVMImage` è possibile selezionare un'immagine di versione per distribuire una nuova macchina virtuale.

L'esempio seguente illustra la sequenza completa di comandi e i relativi output:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Output parziale:

```
PublisherName
-------------
...
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Per l'editore *MicrosoftWindowsServer*:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Output:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Per l'offerta *WindowsServer*:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Output:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
```

Quindi, per lo SKU *2016-Datacenter*:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Ora è possibile combinare l'editore, l'offerta, lo SKU e la versione selezionati in un URN (valori separati da :). Passare questo URN con il parametro `--image` quando si crea una macchina virtuale con il cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). È possibile sostituire il numero di versione nell'URN con "latest" per ottenere la versione più recente dell'immagine.

Se si distribuisce una macchina virtuale con un modello di Resource Manager, impostare i parametri dell'immagine singolarmente nelle proprietà `imageReference`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visualizzare le proprietà del piano

Per visualizzare informazioni sul piano di acquisto di un'immagine, eseguire il cmdlet `Get-AzureRMVMImage`. Se la proprietà `PurchasePlan` nell'output non è `null`, l'immagine presenta condizioni che è necessario accettare prima della distribuzione a livello di codice.  

Ad esempio, l'immagine di *Windows Server 2016 Datacenter* non ha condizioni aggiuntive, quindi l'informazione relativa a `PurchasePlan` è `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Output:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

L'esempio seguente mostra un comando analogo per l'immagine *Data Science Virtual Machine - Windows 2016*, che ha le proprietà `PurchasePlan` seguenti: `name`, `product` e `publisher`. Alcune immagini hanno anche una proprietà `promotion code`. Per distribuire questa immagine, vedere le sezioni seguenti per accettare le condizioni e abilitare la distribuzione a livello di codice.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Output:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Accettare le condizioni

Per visualizzare le condizioni di licenza, usare il cmdlet [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) e passare i parametri del piano di acquisto. L'output include un collegamento alle condizioni di licenza per l'immagine del Marketplace e mostra se le condizioni sono già state accettate in precedenza. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri.

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Output:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Usare il cmdlet [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) per accettare o rifiutare le condizioni. È sufficiente accettare le condizioni per l'immagine una sola volta per ogni sottoscrizione. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri. 

```powershell
$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Output:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Distribuire usando i parametri di piano di acquisto

Dopo avere accettato le condizioni per l'immagine, è possibile distribuire una macchina virtuale nella sottoscrizione. Come illustrato nel frammento di codice seguente, usare il cmdlet [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) per impostare le informazioni sul piano di Azure Marketplace per l'oggetto macchina virtuale. Per uno script completo per creare le impostazioni di rete per la macchina virtuale e completare la distribuzione, vedere gli [esempi di script di PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "0.2.02"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Passare quindi la configurazione della macchina virtuale e gli oggetti di configurazione di rete al cmdlet `New-AzureRmVM`.

## <a name="next-steps"></a>Passaggi successivi
Per creare rapidamente una macchina virtuale con il cmdlet `New-AzureRmVM` usando le informazioni di base sull'immagine, vedere [Creare una macchina virtuale Windows con PowerShell](quick-create-powershell.md).

Vedere un esempio di script di PowerShell per [creare una macchina virtuale completamente configurata](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


