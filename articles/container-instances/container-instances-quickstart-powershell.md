---
title: Avvio rapido - Distribuire un contenitore Docker in Istanze di Azure Container - PowerShell
description: In questo argomento di avvio rapido viene usato Azure PowerShell per distribuire rapidamente un'app Web che viene eseguita in un'istanza di contenitore di Azure isolata
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c6baf10308f04d5f08ba651bd70ac2b48dfc013c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729461"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Guida introduttiva: Distribuire un'istanza di contenitore in Azure con Azure PowerShell

Il servizio Istanze di Azure Container consente di eseguire contenitori Docker serverless in Azure in modo semplice e rapido. Distribuire un'applicazione in un'istanza di contenitore su richiesta quando non è necessaria una piattaforma di orchestrazione di contenitori completa come il servizio Azure Kubernetes.

In questo argomento di avvio rapido viene usato Azure PowerShell per distribuire un contenitore Windows isolato e renderne disponibile l'applicazione con un nome di dominio completo (FQDN). Pochi secondi dopo aver eseguito un singolo comando di distribuzione, è possibile passare all'applicazione in esecuzione nel contenitore:

![App distribuita in Istanze di Azure Container visualizzata nel browser][qs-powershell-01]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di contenitore di Azure, analogamente a tutte le risorse di Azure, devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

Per iniziare, creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* con il comando [New-AzResourceGroup][New-AzResourceGroup] seguente:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Creare un contenitore

Dopo aver creato il gruppo di risorse, è possibile eseguire un contenitore in Azure. Per creare un'istanza di contenitore con Azure PowerShell, specificare il nome del gruppo di risorse, il nome dell'istanza di contenitore e l'immagine del contenitore Docker nel cmdlet [New-AzContainerGroup][New-AzContainerGroup]. In questo argomento di avvio rapido viene usata l'immagine `mcr.microsoft.com/windows/servercore/iis:nanoserver` pubblica. Questa immagine include Microsoft Internet Information Services (IIS) per l'esecuzione in Nano Server.

È possibile esporre i contenitori in Internet specificando una o più porte da aprire, un'etichetta del nome DNS o entrambi. In questa guida introduttiva si distribuisce un contenitore con un'etichetta del nome DNS in modo che IIS sia raggiungibile pubblicamente.

Per avviare un'istanza di contenitore, eseguire un comando simile al seguente. Impostare un valore di `-DnsNameLabel` univoco all'interno dell'area di Azure in cui si crea l'istanza. Se si riceve il messaggio di errore "L'etichetta del nome DNS non è disponibile", provare con un'etichetta del nome DNS diversa.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Entro pochi secondi si dovrebbe ricevere una risposta da Azure. Inizialmente, il valore `ProvisioningState` del contenitore è **Creazione in corso**, ma dovrebbe passare a **Operazione completata** entro un minuto o due. Controllare lo stato di distribuzione con il cmdlet [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Lo stato di provisioning, il nome di dominio completo (FQDN) e l'indirizzo IP del contenitore vengono visualizzati nell'output del cmdlet:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


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

Quando il valore `ProvisioningState` del contenitore è **Operazione completata**, passare al relativo indirizzo `Fqdn` nel browser. Se viene visualizzata una pagina Web simile all'immagine seguente, congratulazioni! È stata completata la distribuzione di un'applicazione in esecuzione in un contenitore Docker in Azure.

![IIS distribuito con Istanze di Azure Container visualizzato nel browser][qs-powershell-01]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il contenitore non è più necessario, rimuoverlo con il cmdlet [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di contenitore di Azure da un'immagine nel registro nell'hub Docker pubblico. Per provare a creare un'immagine del contenitore e a distribuirla da un registro contenitori di Azure privato, passare all'esercitazione su Istanze di Azure Container.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di Azure Container](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
