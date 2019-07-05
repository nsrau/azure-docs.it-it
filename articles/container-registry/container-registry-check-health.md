---
title: Verificare l'integrità del Registro di sistema in Registro contenitori di Azure
description: Informazioni su come eseguire un comando rapido diagnostica per identificare i problemi comuni quando si usa un registro contenitori di Azure, tra cui locale di configurazione di Docker e la connettività al Registro di sistema
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555098"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verificare l'integrità di un registro contenitori di Azure

Quando si usa un registro contenitori di Azure, in alcuni casi potrebbero verificarsi problemi. Ad esempio, potrebbe non essere in grado di estrarre un'immagine del contenitore a causa di un problema con Docker nell'ambiente locale. In alternativa, un problema di rete potrebbe impedire la connessione al Registro di sistema. 

Come primo passaggio di diagnostica, eseguire la [az acr check-health][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Eseguire az acr controllo integrità

Gli esempi seguenti mostrano diverse modalità per eseguire il `az acr check-health` comando.

> [!NOTE]
> Se si esegue il comando in Azure Cloud Shell, l'ambiente locale non è selezionata. Tuttavia, è possibile controllare l'accesso a un registro di sistema di destinazione.

### <a name="check-the-environment-only"></a>Controllare solo l'ambiente

Per controllare il Docker locale daemon, la versione della riga di comando e configurazione di client Helm, eseguire il comando senza parametri aggiuntivi:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Controllare l'ambiente e un registro di sistema di destinazione

Per controllare l'accesso a un registro di sistema, nonché eseguire controlli nell'ambiente locale, passare il nome di un registro di sistema di destinazione. Ad esempio:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Segnalazione errori

Il comando registra le informazioni nell'output standard. Se viene rilevato un problema, fornisce un codice di errore e una descrizione. Per altre informazioni sui codici e possibili soluzioni, vedere la [riferimento errore](container-registry-health-error-reference.md).

Per impostazione predefinita, il comando Arresta ogni volta che viene rilevato un errore. È anche possibile eseguire il comando in modo che fornisce output per tutti i controlli di integrità, anche se vengono rilevati errori. Aggiungere il `--ignore-errors` parametro, come illustrato negli esempi seguenti:

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

Per informazioni dettagliate sui codici di errore restituiti dal [az acr controllo dell'integrità della][az-acr-check-health] comando, vedere la [riferimento Errore controllo integrità](container-registry-health-error-reference.md).

Vedere le [domande frequenti su](container-registry-faq.md) per domande frequenti e altri problemi noti sul registro contenitori di Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
