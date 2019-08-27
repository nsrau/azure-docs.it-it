---
title: Autenticazione al Registro Azure Container con entità servizio
description: Consentire l'accesso alle immagini del registro contenitori privato usando un'entità servizio Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032363"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticazione al Registro Azure Container con entità servizio

È possibile usare un'entità servizio Azure Active Directory (Azure AD) per fornire l'accesso `docker push` e `pull` dell'immagine del contenitore al registro contenitori. Se si usa un'entità servizio, è possibile fornire l'accesso ai servizi e alle applicazioni "headless".

## <a name="what-is-a-service-principal"></a>Che cos'è un'entità servizio?

Le *entità servizio* di Azure AD forniscono accesso alle risorse Azure all'interno della sottoscrizione. È possibile considerare un'entità servizio come identità utente per un servizio, dove "servizio" è qualsiasi applicazione, servizio o piattaforma che deve accedere alle risorse. È possibile configurare un'entità servizio con diritti di accesso limitati solo alle risorse specificate. Configurare quindi l'applicazione o il servizio in modo che usi le credenziali dell'entità servizio per accedere a tali risorse.

Nel contesto del Registro Azure Container è possibile creare un'entità servizio Azure AD con autorizzazioni pull, push e pull o con altre autorizzazioni per il registro Docker privato in Azure. Per l'elenco completo, vedere [Azure Container Registry roles and permissions](container-registry-roles.md) (Ruoli e autorizzazioni del Registro Azure Container).

## <a name="why-use-a-service-principal"></a>Perché usare un'entità servizio?

Usando un'entità servizio Azure AD, è possibile fornire un accesso limitato al registro di sistema del contenitore privato. Creare entità servizio diverse per ogni applicazione o servizio, ciascuna con diritti di accesso personalizzati al registro di sistema. Poiché è possibile evitare di condividere le credenziali tra servizi e applicazioni, è possibile ruotare le credenziali o revocare l'accesso solamente all'entità servizio (e quindi all'applicazione) scelta.

Ad esempio, configurare l'applicazione Web in modo che usi un'entità servizio che la fornisca `pull` solo l'accesso alle immagini, mentre il sistema di compilazione usa un'entità servizio che `push` fornisce `pull` l'accesso sia a che a. Se lo sviluppo dell'applicazione cambia proprietario, è possibile ruotare le credenziali dell'entità servizio senza alcun impatto sul sistema di compilazione.

## <a name="when-to-use-a-service-principal"></a>Quando usare un'entità servizio

Usare un'entità servizio per fornire accesso al registro negli **scenari headless**, ovvero per qualsiasi applicazione, servizio o script che deve eseguire le operazioni di push o pull delle immagini del contenitore in modo automatico. Esempio:

  * *Pull*: distribuire contenitori da un registro a sistemi di orchestrazione, inclusi Kubernetes, DC/OS e Docker Swarm. È anche possibile eseguire il pull da registri contenitori a servizi di Azure correlati, ad esempio [Azure Kubernetes Service (AKS)](container-registry-auth-aks.md), [istanze di contenitore di Azure](container-registry-auth-aci.md), [servizio app](../app-service/index.yml), [batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e altri.

  * *Push*: creare immagini dei contenitori ed eseguirne il push in un registro usando soluzioni di integrazione e distribuzione continua, come Azure Pipelines o Jenkins.

Per l'accesso singolo a un registro di sistema, ad esempio quando si esegue il pull manuale di un'immagine del contenitore nella workstation di sviluppo, è consigliabile usare la propria [identità di Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) invece per l'accesso al registro di sistema, ad esempio con [AZ ACR login][az-acr-login].

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Script di esempio

È possibile trovare gli script di esempio precedenti per l'interfaccia della riga di comando di Azure in GitHub, nonché le versioni per Azure PowerShell:

* [Interfaccia della riga di comando di Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Eseguire l'autenticazione con l'entità servizio

Quando si dispone di un'entità servizio a cui è stato concesso l'accesso al registro contenitori, è possibile configurarne le credenziali per l'accesso a servizi e applicazioni "privi di intestazioni `docker login` " oppure immetterli usando il comando. Usare i valori seguenti:

* **Nome utente** : ID dell'applicazione dell'entità servizio (noto anche come *ID client*)
* **Password** : password dell'entità servizio (detta anche *segreto client*)

Ogni valore è un GUID del form `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> È possibile rigenerare la password di un'entità servizio eseguendo il comando [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset).
>

### <a name="use-credentials-with-azure-services"></a>Usare le credenziali con i servizi di Azure

È possibile usare le credenziali dell'entità servizio di qualsiasi servizio di Azure che può eseguire l'autenticazione con un Registro Azure Container. Ecco alcuni esempi:

* [Eseguire l'autenticazione con Registro Azure Container dal servizio Azure Kubernetes](container-registry-auth-aks.md)
* [Eseguire l'autenticazione con Registro Azure Container da Istanze di Azure Container](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Usare con l'account di accesso di Docker

È anche possibile eseguire `docker login` usando un'entità servizio. Nell'esempio seguente, l'ID applicazione dell'entità servizio viene passato nella variabile `$SP_APP_ID`di ambiente e la password nella variabile. `$SP_PASSWD` Per le procedure consigliate per gestire le credenziali Docker, vedere la Guida di riferimento al comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Una volta effettuato l'accesso, Docker memorizza nella cache le credenziali.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica dell'autenticazione](container-registry-authentication.md) per altri scenari di autenticazione con un registro contenitori di Azure.

* Per un esempio di come usare un insieme di credenziali delle chiavi di Azure per archiviare e recuperare le credenziali dell'entità servizio per un registro contenitori, vedere l'esercitazione per [compilare e distribuire un'immagine del contenitore con le attività di ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
