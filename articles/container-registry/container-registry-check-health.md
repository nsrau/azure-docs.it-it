---
title: Controllare l'integrità del Registro di sistema
description: Informazioni su come eseguire un comando di diagnostica rapida per identificare i problemi comuni quando si usa un registro contenitori di Azure, inclusa la configurazione locale di Docker e la connettività al Registro di sistema
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456427"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Controllare l'integrità di un registro contenitori di AzureCheck the health of an Azure container registry

Quando si usa un registro contenitori di Azure, in alcuni casi potrebbero verificarsi problemi. Ad esempio, potrebbe non essere possibile estrarre un'immagine del contenitore a causa di un problema con Docker nell'ambiente locale. In alternativa, un problema di rete potrebbe impedire la connessione al Registro di sistema. 

Come primo passaggio di diagnostica, eseguire il comando [az acr check-health][az-acr-check-health] per ottenere informazioni sull'integrità dell'ambiente e, facoltativamente, l'accesso a un Registro di sistema di destinazione. Questo comando è disponibile nell'interfaccia della riga di comando di Azure versione 2.0.67 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="run-az-acr-check-health"></a>Eseguire az acr check-health

Negli esempi seguenti vengono illustrati `az acr check-health` diversi modi per eseguire il comando.

> [!NOTE]
> Se si esegue il comando in Azure Cloud Shell, l'ambiente locale non viene selezionato. Tuttavia, è possibile controllare l'accesso a un registro di destinazione.

### <a name="check-the-environment-only"></a>Controllare solo l'ambiente

Per controllare la configurazione del client Docker locale, della versione CLI e del client Helm, eseguire il comando senza parametri aggiuntivi:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Controllare l'ambiente e un registro di destinazioneCheck the environment and a target registry

Per controllare l'accesso a un Registro di sistema ed eseguire controlli dell'ambiente locale, passare il nome di un Registro di sistema di destinazione. Ad esempio:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Errore di segnalazione

Il comando registra le informazioni nell'output standard. Se viene rilevato un problema, fornisce un codice di errore e una descrizione. Per ulteriori informazioni sui codici e sulle possibili soluzioni, vedere le [informazioni di riferimento sull'errore](container-registry-health-error-reference.md).

Per impostazione predefinita, il comando si arresta ogni volta che rileva un errore. È anche possibile eseguire il comando in modo che fornisca l'output per tutti i controlli di integrità, anche se vengono rilevati errori. Aggiungere `--ignore-errors` il parametro, come illustrato negli esempi seguenti:Add the parameter, as shown in the following examples:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Output di esempio:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sui codici di errore restituiti dal comando [az acr check-health][az-acr-check-health] , vedere il riferimento all'errore del [controllo di integrità](container-registry-health-error-reference.md).

Vedere le [domande frequenti](container-registry-faq.md) per le domande frequenti e altri problemi noti sul Registro di sistema del contenitore di Azure.See the FAQ for frequently asked questions and other known issues about Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
