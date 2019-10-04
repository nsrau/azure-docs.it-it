---
title: Controllare l'integrità del registro di sistema in Azure Container Registry
description: Informazioni su come eseguire un rapido comando di diagnostica per identificare i problemi comuni quando si usa un registro contenitori di Azure, tra cui la configurazione Docker locale e la connettività al registro di sistema
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309725"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verificare l'integrità di un registro contenitori di Azure

Quando si usa un registro contenitori di Azure, è possibile che si verifichino occasionalmente problemi. Ad esempio, potrebbe non essere possibile eseguire il pull di un'immagine del contenitore a causa di un problema con Docker nell'ambiente locale. In alternativa, un problema di rete potrebbe impedire la connessione al registro di sistema. 

Come primo passaggio di diagnostica, eseguire il comando [AZ ACR check-Health][az-acr-check-health] per ottenere informazioni sullo stato dell'ambiente e, facoltativamente, per accedere a un registro di sistema di destinazione. Questo comando è disponibile nell'interfaccia della riga di comando di Azure versione 2.0.67 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="run-az-acr-check-health"></a>Esecuzione AZ ACR check-Health

Negli esempi seguenti vengono illustrati diversi modi per `az acr check-health` eseguire il comando.

> [!NOTE]
> Se si esegue il comando in Azure Cloud Shell, l'ambiente locale non è selezionato. Tuttavia, è possibile controllare l'accesso a un registro di sistema di destinazione.

### <a name="check-the-environment-only"></a>Controllare solo l'ambiente

Per controllare il daemon Docker locale, la versione dell'interfaccia della riga di comando e la configurazione del client Helm, eseguire il comando senza parametri aggiuntivi:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Controllare l'ambiente e un registro di sistema di destinazione

Per controllare l'accesso a un registro di sistema e per eseguire controlli dell'ambiente locale, passare il nome di un registro di destinazione. Ad esempio:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Segnalazione errori

Il comando registra le informazioni nell'output standard. Se viene rilevato un problema, viene fornito un codice di errore e una descrizione. Per ulteriori informazioni sui codici e sulle possibili soluzioni, vedere il [riferimento dell'errore](container-registry-health-error-reference.md).

Per impostazione predefinita, il comando viene interrotto ogni volta che viene rilevato un errore. È anche possibile eseguire il comando in modo che fornisca l'output per tutti i controlli di integrità, anche se vengono rilevati errori. Aggiungere il `--ignore-errors` parametro, come illustrato negli esempi seguenti:

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

Per informazioni dettagliate sui codici di errore restituiti dal comando [AZ ACR check-Health][az-acr-check-health] , vedere informazioni di [riferimento sull'errore di controllo integrità](container-registry-health-error-reference.md).

Vedere le domande [frequenti](container-registry-faq.md) sulle domande frequenti e altri problemi noti relativi ad Azure container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
