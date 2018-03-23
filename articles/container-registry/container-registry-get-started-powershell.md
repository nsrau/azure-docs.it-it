---
title: Guida introduttiva - Creare un registro contenitori Docker privati in Azure con PowerShell
description: Imparare rapidamente a creare un registro contenitori Docker privati con PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2bae45955cf3c2b157acce2544b1f35fbddd0170
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Creare un Registro contenitori di Azure usando PowerShell

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro contenitori di Azure tramite PowerShell, l'inserimento di un'immagine del contenitore nel registro e infine la distribuzione del contenitore dal registro in Istanze di contenitore di Azure.

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Creare un'istanza di record di controllo di accesso con il comando [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nell'esempio seguente viene usato il nome *myContainerRegistry007*. Aggiornarlo a un valore univoco.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Accedere al record di controllo di accesso

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. Innanzitutto, usare il comando [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) per ottenere le credenziali di amministratore per l'istanza del record di controllo di accesso.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Usare il comando [docker login][docker-login] per accedere all'istanza record di controllo di accesso.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. È possibile visualizzare un avviso di sicurezza in cui si consiglia l'uso del parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se necessario, eseguire il comando seguente per eseguire il pull di un'immagine creata in precedenza dall'Hub Docker.

```powershell
docker pull microsoft/aci-helloworld
```

L'immagine deve essere contrassegnata con il nome del server di accesso del record di controllo di accesso. A tale scopo, usare il comando [docker tag][docker-tag]. 

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Usare infine [docker push][docker-push] per eseguire il push dell'immagine in Istanze di contenitore di Azure.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Distribuire l'immagine in Istanze di contenitore di Azure
Per distribuire l'immagine come istanza di contenitore in Istanze di contenitore di Azure, convertire prima la credenziale del registro in PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Per distribuire l'immagine del contenitore dal registro contenitori con 1 core CPU e 1 GB di memoria, eseguire questo comando:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Si riceverà una risposta iniziale da Azure Resource Manager con i dettagli del contenitore. Per monitorare lo stato del contenitore e verificare quando è in esecuzione, ripetere il comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Per il completamento dovrebbe essere necessario meno di un minuto.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Output di esempio: `Succeeded`

## <a name="view-the-application"></a>Visualizzare l'applicazione
Dopo aver completato la distribuzione in Istanze di contenitore di Azure, recuperare l'indirizzo IP pubblico del contenitore con il comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Output di esempio: `"13.72.74.222"`

Per visualizzare l'applicazione in esecuzione, esplorare l'indirizzo IP pubblico nel browser preferito. Dovrebbe essere visualizzata una schermata analoga alla seguente:

![App Hello World nel browser][qs-portal-15]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, l'istanza di Registro contenitori di Azure e tutte le istanze di contenitore di Azure usando il comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup].

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un registro contenitori di Azure con l'interfaccia della riga di comando di Azure e ne è stata avviata un'istanza in Istanze di contenitore di Azure. Passare all'esercitazione su Istanze di contenitore di Azure per maggiori informazioni.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
