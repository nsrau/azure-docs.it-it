---
title: Avvio rapido - Creare il primo contenitore di Istanze di contenitore di Azure con PowerShell
description: In questa guida introduttiva si userà Azure PowerShell per distribuire un contenitore Windows in Istanze di contenitore di Azure
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6e43a525010ac57a75f6db4c43f6f1407dab93a8
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Guida introduttiva: Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e gestione di contenitori Docker in Azure, senza dover eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore. In questa guida introduttiva si creerà un contenitore Windows in Azure che verrà quindi esposto in Internet con un nome di dominio completo (FQDN). Per completare questa operazione, è sufficiente un solo comando. Entro pochi minuti sarà possibile vedere l'applicazione in esecuzione nel browser:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][qs-powershell-01]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.5 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Creare un contenitore

È possibile creare un contenitore specificando un nome, un'immagine Docker e un gruppo di risorse di Azure nel cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Facoltativamente, è possibile esporre il contenitore in Internet con un'etichetta del nome DNS.

Eseguire questo comando per avviare un contenitore Nano Server che esegue Internet Information Services (IIS). Il valore `-DnsNameLabel` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza, quindi potrebbe essere necessario modificare questo valore per garantire l'univocità.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Entro pochi secondi si dovrebbe ricevere una risposta alla richiesta. Il contenitore inizialmente presenta lo stato **Creating**, ma verrà avviato entro un minuto o due. È possibile controllare lo stato di distribuzione con il cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Lo stato di provisioning, il nome di dominio completo (FQDN) e l'indirizzo IP del contenitore vengono visualizzati nell'output del cmdlet:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Quando il valore di **ProvisioningState** del contenitore diventa `Succeeded`, passare al relativo `Fqdn` nel browser:

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

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
