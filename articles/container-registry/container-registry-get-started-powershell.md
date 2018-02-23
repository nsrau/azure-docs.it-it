---
title: Guida introduttiva - Creare un registro contenitori Docker privati in Azure con PowerShell
description: Imparare rapidamente a creare un registro contenitori Docker privati con PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 80b5055dee35cd6efe62ee949c05aef386a3ba14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Creare un Registro contenitori di Azure usando PowerShell

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro contenitori di Azure con PowerShell.

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

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. È possibile visualizzare un avviso di sicurezza in cui si consiglia l'uso del parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se necessario, eseguire il comando seguente per eseguire il pull di un'immagine creata in precedenza dall'Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

L'immagine deve essere contrassegnata con il nome del server di accesso del record di controllo di accesso. Eseguire il comando [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) per restituire il nome del server di accesso dell'istanza del record di controllo di accesso.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire *acrLoginServer* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Infine, usare [docker push][docker-push] per eseguire il push delle immagini nell'istanza del record di controllo di accesso. Sostituire *acrLoginServer* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, l'istanza del record di controllo di accesso e tutte le immagini del contenitore non sono più necessari è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida è stato creato un Registro contenitori di Azure con l'interfaccia della riga di comando di Azure. Se si desidera usare il Registro contenitori di Azure con le istanze di contenitore di Azure, continuare con l'esercitazione relativa alle istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/