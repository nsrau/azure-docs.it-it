---
title: File di inclusione per PowerShell per DNS di Azure
description: File di inclusione per PowerShell per DNS di Azure
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 388bea528b138d78b9ec23ceea295108306c61e9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613603"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurare Azure PowerShell per DNS di Azure

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* È necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

Inoltre, per usare Zone private (anteprima pubblica), è necessario assicurarsi di disporre dei moduli e delle versioni di PowerShell riportati di seguito. 
* AzureRM.Dns - [versione 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) o successiva
* AzureRM.Network - [versione 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) o successiva

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
L'output dei comandi precedenti deve indicare che la versione di AzureRM.Dns è 4.1.0 o versione successiva e che quella di AzureRM.Network è 5.4.0 o versione successiva.  

Nel caso in cui il sistema disponga di versioni precedenti, è possibile installare la versione più recente di Azure PowerShell o scaricare e installare i moduli precedenti da PowerShell Gallery, usando i collegamenti riportati sopra accanto alle versioni dei moduli. È quindi possibile installarli usando i comandi riportati di seguito. Entrambi i moduli sono necessari e sono pienamente compatibili con le versioni precedenti. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Aprire la console di PowerShell e connettersi al proprio account. Per altre informazioni, vedere [Uso di PowerShell con Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selezionare la sottoscrizione
 
Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Scegliere quali sottoscrizioni Azure usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrare il provider di risorse

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. Per poter usare il servizio DNS di Azure, la sottoscrizione di Azure deve essere registrata per l'uso di questo provider di risorse. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
