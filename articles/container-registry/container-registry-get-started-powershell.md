---
title: Guida introduttiva - Creare un registro contenitori Docker privati in Azure - PowerShell
description: Imparare rapidamente a creare un registro contenitori Docker privati in Azure con PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299744"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Guida introduttiva: Creare un registro contenitori privato usando Azure PowerShell

Registro Azure Container è un servizio gestito e privato di registri contenitori Docker usato per compilare, archiviare e servire immagini del contenitore Docker. In questa guida introduttiva si apprenderà come creare un registro contenitori di Azure usando PowerShell. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

## <a name="prerequisites"></a>Prerequisiti

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 5.7.0 o successiva. Per determinare la versione installata eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

È anche necessario avere Docker installato localmente. Docker offre pacchetti per i sistemi [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Poiché Azure Cloud Shell non include tutti i componenti di Docker necessari, ovvero il daemon `dockerd`, non è possibile usare Cloud Shell lo per questa guida rapida.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzureRmAccount][Connect-AzureRmAccount] e seguire le indicazioni visualizzate.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Dopo aver eseguito l'autenticazione con Azure, creare un gruppo di risorse con il comando [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Creare un registro contenitori

Successivamente creare un registro contenitori nel nuovo gruppo di risorse con il comando [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. L'esempio seguente crea un registro denominato "myContainerRegistry007". Sostituire *myContainerRegistry007* nel comando seguente, quindi eseguirlo per creare il registro:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

In questa guida introduttiva viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [SKU di Registro Azure Container][container-registry-skus].

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere al registro. Negli scenari di produzione è necessario usare una singola identità o un'entità servizio per l'accesso al registro contenitori, ma per brevità in questa guida introduttiva è sufficiente abilitare l'utente amministratore nel registro con il comando [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential]:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Successivamente eseguire [docker login][docker-login] per eseguire l'accesso:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo avere usato le risorse create in questa guida rapida, usare il comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate in tale registro:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di Registro Azure Container con Azure PowerShell, è stato eseguito il push di un'immagine del contenitore e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md