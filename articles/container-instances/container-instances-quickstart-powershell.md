---
title: Avvio rapido - Creare il primo contenitore di Istanze di contenitore di Azure con PowerShell
description: Introduzione a Istanze di contenitore di Azure on la creazione di un'istanza di contenitore di Windows con PowerShell.
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: ca10274fc6a23d7f5e7436dbaf72a6e7a918f275
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e gestione di contenitori Docker in Azure, senza dover eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore.

In questa guida introduttiva viene descritta la procedura per creare un contenitore di Windows in Azure ed esporlo a Internet con un indirizzo IP pubblico. Per completare questa operazione, è sufficiente un solo comando. Entro pochi minuti sarà possibile visualizzare l'applicazione in esecuzione nel browser:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][qs-powershell-01]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Creare un contenitore

È possibile creare un contenitore specificando un nome, un'immagine Docker e un gruppo di risorse di Azure nel cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. È facoltativamente possibile esporre il contenitore a Internet con un indirizzo IP pubblico. In questo caso, si userà un contenitore Nano Server di Windows in cui viene eseguito Internet Information Services (IIS).

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Dopo alcuni secondi verrà restituita una risposta alla richiesta. Il contenitore inizialmente presenta lo stato **Creazione in corso**, ma viene avviato entro un paio di minuti. È possibile controllare lo stato con il cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Lo stato di provisioning e l'indirizzo IP del contenitore vengono visualizzati nell'output del cmdlet:

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Quando **ProvisioningState** del contenitore passa a `Succeeded`, è possibile raggiungerlo nel browser usando l'indirizzo IP fornito.

![IIS distribuito con Istanze di contenitore di Azure visualizzato nel browser][qs-powershell-01]

## <a name="delete-the-container"></a>Eliminare il contenitore

Quando il contenitore non è più necessario, è possibile rimuoverlo con il cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato avviato un contenitore di Windows predefinito in Istanze di contenitore di Azure. Per provare a creare personalmente e distribuire un contenitore in Istanze di contenitore di Azure tramite Registro contenitori di Azure, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png