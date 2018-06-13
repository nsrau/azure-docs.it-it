---
title: Guida introduttiva - Creare un registro contenitori Docker privati in Azure con PowerShell
description: Imparare rapidamente a creare un registro contenitori Docker privati in Azure con PowerShell.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885120"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Guida introduttiva: Creare un Registro contenitori di Azure usando PowerShell

Registro contenitori di Azure è un servizio gestito e privato di registri contenitori Docker usato per compilare, archiviare e servire immagini del contenitore Docker. In questa guida introduttiva si apprenderà a creare un registro contenitori di Azure usando PowerShell. Dopo aver creato il registro, eseguire il push di un'immagine del contenitore in esso, quindi distribuire il contenitore dal registro in Istanze di contenitore di Azure.

## <a name="prerequisites"></a>prerequisiti

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 5.7.0 o successiva. Per determinare la versione installata eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

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

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere al registro. Usare il comando [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] per ottenere innanzitutto le credenziali di amministratore per il registro:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Successivamente eseguire [docker login][docker-login] per eseguire l'accesso:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Un'operazione di accesso corretta restituisce `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Eseguire il push dell'immagine nel registro

Dopo aver effettuato l'accesso al registro, è possibile eseguire il push delle immagini del contenitore in esso. Per ottenere un'immagine è possibile eseguire il push nel registro e il pull dell'immagine pubblica [aci-helloworld][aci-helloworld-image] dall'hub Docker. L'immagine [aci-helloworld][aci-helloworld-github] è una piccola applicazione Node.js che serve una pagina HTML statica che mostra il logo di Istanze di contenitore di Azure.

```powershell
docker pull microsoft/aci-helloworld
```

Quando si esegue il pull dell'immagine, l'output è simile al seguente:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Prima di poter eseguire il push di un'immagine nel registro contenitori di Azure, è necessario contrassegnarla con il nome di dominio completo, ovvero FQDN, del registro. L'FQDN dei registri contenitore di Azure sono nel formato *\<registry-name\>.azurecr.io*.

Popolare una variabile con il tag completo dell'immagine. Includere il server di accesso, il nome del repository ("aci-helloworld") e la versione dell'immagine ("v1"):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

A questo punto contrassegnare l'immagine con [docker tag][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

E infine eseguire il [push di Docker][docker-push] nel registro:

```powershell
docker push $image
```

Quando il client Docker esegue il push dell'immagine, l'output dovrebbe essere simile al seguente:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Congratulazioni! È stato appena eseguito il push dell'immagine del contenitore nel registro.

## <a name="deploy-image-to-aci"></a>Distribuire l'immagine in Istanze di contenitore di Azure

Ora che l'immagine si trova nel registro, distribuire il contenitore direttamente in Istanze di contenitore di Azure per visualizzarlo in esecuzione in Azure.

Convertire innanzitutto le credenziale del registro in *PSCredential*. Per il comando `New-AzureRmContainerGroup`, che consente di creare l'istanza del contenitore, è necessario usare questo formato.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

In aggiunta l'etichetta del nome DNS per il contenitore deve essere univoca all'interno dell'area di Azure in cui viene creata. Eseguire il comando seguente per popolare una variabile con un nome generato:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Infine eseguire [New-AzureRmContainerGroup][New-AzureRmContainerGroup] per distribuire un contenitore dall'immagine nel registro con una CPU da 1 core e 1 GB di memoria:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Si riceverà una risposta iniziale da Azure con i dettagli del contenitore e lo stato inizialmente è "In sospeso":

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Per monitorarne lo stato e determinare quando è in esecuzione, ripetere il comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] più di una volta. L'avvio del contenitore dovrebbe richiedere meno di un minuto.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

In questo caso è possibile vedere che ProvisioningState del contenitore è inizialmente *Creazione in corso* e quando è attivo e in esecuzione diventa *Operazione riuscita*:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Visualizzare un'applicazione in esecuzione

Dopo aver eseguito la distribuzione in Istanze di contenitore di Azure e quando il contenitore è attivo e in esecuzione, passare al nome di dominio completo, ovvero FQDN, nel browser per visualizzare l'app in esecuzione in Azure.

Ottenere l'FQDN per il contenitore con [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

L'output del comando è l'FQDN dell'istanza del contenitore:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Dopo aver ottenuto l'FQDN, esplorarlo nel browser:

![App Hello World nel browser][qs-psh-01-running-app]

Congratulazioni! Il contenitore esegue un'applicazione in Azure, distribuito direttamente da un'immagine del contenitore nel registro contenitori privato di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo avere usato le risorse create in questa guida rapida, usare il comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] per rimuovere il gruppo di risorse, il registro contenitori e l'istanza del contenitore:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un registro contenitori di Azure con l'interfaccia della riga di comando di Azure e ne è stata avviata un'istanza in Istanze di contenitore di Azure. Passare all'esercitazione su Istanze di contenitore di Azure per maggiori informazioni.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
