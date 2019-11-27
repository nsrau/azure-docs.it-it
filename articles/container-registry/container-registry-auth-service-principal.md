---
title: Eseguire l'autenticazione con l'entità servizio
description: Consentire l'accesso alle immagini del registro contenitori privato usando un'entità servizio Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455400"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticazione al Registro Azure Container con entità servizio

È possibile usare un'entità servizio Azure Active Directory (Azure AD) per fornire l'accesso `docker push` e `pull` dell'immagine del contenitore al registro contenitori. Se si usa un'entità servizio, è possibile fornire l'accesso ai servizi e alle applicazioni "headless".

## <a name="what-is-a-service-principal"></a>Che cos'è un'entità servizio?

Le *entità servizio* di Azure AD forniscono accesso alle risorse Azure all'interno della sottoscrizione. È possibile considerare un'entità servizio come identità utente per un servizio, dove "servizio" è qualsiasi applicazione, servizio o piattaforma che deve accedere alle risorse. È possibile configurare un'entità servizio con diritti di accesso limitati solo alle risorse specificate. Configurare quindi l'applicazione o il servizio in modo che usi le credenziali dell'entità servizio per accedere a tali risorse.

Nel contesto del Registro Azure Container è possibile creare un'entità servizio Azure AD con autorizzazioni pull, push e pull o con altre autorizzazioni per il registro Docker privato in Azure. Per l'elenco completo, vedere [Azure Container Registry roles and permissions](container-registry-roles.md) (Ruoli e autorizzazioni del Registro Azure Container).

## <a name="why-use-a-service-principal"></a>Perché usare un'entità servizio?

Usando un'entità servizio Azure AD, è possibile fornire un accesso limitato al registro di sistema del contenitore privato. Creare entità servizio diverse per ogni applicazione o servizio, ciascuna con diritti di accesso personalizzati al registro di sistema. Poiché è possibile evitare di condividere le credenziali tra servizi e applicazioni, è possibile ruotare le credenziali o revocare l'accesso solamente all'entità servizio (e quindi all'applicazione) scelta.

Ad esempio, configurare l'applicazione Web in modo che utilizzi un'entità servizio che la fornisca solo l'accesso con immagine `pull`, mentre il sistema di compilazione usa un'entità servizio che fornisce l'accesso sia `push` che `pull`. Se lo sviluppo dell'applicazione cambia mano, è possibile ruotare le credenziali dell'entità servizio senza influire sul sistema di compilazione.

## <a name="when-to-use-a-service-principal"></a>Quando usare un'entità servizio

Usare un'entità servizio per fornire accesso al registro negli **scenari headless**, ovvero per qualsiasi applicazione, servizio o script che deve eseguire le operazioni di push o pull delle immagini del contenitore in modo automatico. Ad esempio:

  * *Pull*: distribuire i contenitori da un registro ai sistemi di orchestrazione, tra cui KUBERNETES, DC/OS e Docker Swarm. È anche possibile eseguire il pull da registri contenitori a servizi di Azure correlati, ad esempio [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [istanze di contenitore di Azure](container-registry-auth-aci.md), [servizio app](../app-service/index.yml), [batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e altri.

  * *Push*: compilare immagini del contenitore ed eseguirne il push in un registro usando soluzioni di distribuzione e integrazione continua come Azure Pipelines o Jenkins.

Per l'accesso singolo a un registro di sistema, ad esempio quando si esegue il pull manuale di un'immagine del contenitore nella workstation di sviluppo, è consigliabile usare la propria [identità di Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) invece per l'accesso al registro di sistema, ad esempio con [AZ ACR login][az-acr-login].

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Script di esempio

È possibile trovare gli script di esempio precedenti per l'interfaccia della riga di comando di Azure in GitHub, nonché le versioni per Azure PowerShell:

* [Interfaccia della riga di comando di Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Eseguire l'autenticazione con l'entità servizio

Quando si dispone di un'entità servizio a cui è stato concesso l'accesso al registro contenitori, è possibile configurarne le credenziali per l'accesso a servizi e applicazioni "privi di intestazioni" oppure immetterli usando il comando `docker login`. Usare i valori seguenti:

* **Nome utente** : ID dell'applicazione dell'entità servizio (noto anche come *ID client*)
* **Password** : password dell'entità servizio (detta anche *segreto client*)

Ogni valore è un GUID del form `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> È possibile rigenerare la password di un'entità servizio eseguendo il comando [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset).
>

### <a name="use-credentials-with-azure-services"></a>Usare le credenziali con i servizi di Azure

È possibile usare le credenziali dell'entità servizio da qualsiasi servizio di Azure che esegue l'autenticazione con un registro contenitori di Azure.  Usare le credenziali dell'entità servizio al posto delle credenziali di amministratore del registro di sistema per diversi scenari.

Ad esempio, usare le credenziali per eseguire il pull di un'immagine da un registro contenitori di Azure a [istanze di contenitore di Azure](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Usare con l'account di accesso di Docker

È possibile eseguire `docker login` usando un'entità servizio. Nell'esempio seguente l'ID applicazione dell'entità servizio viene passato nella variabile di ambiente `$SP_APP_ID`e la password nella variabile `$SP_PASSWD`. Per le procedure consigliate per gestire le credenziali Docker, vedere la Guida di riferimento al comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Una volta effettuato l'accesso, Docker memorizza nella cache le credenziali.

### <a name="use-with-certificate"></a>USA con certificato

Se è stato aggiunto un certificato all'entità servizio, è possibile accedere all'interfaccia della riga di comando di Azure con l'autenticazione basata su certificati, quindi usare il comando [AZ ACR login][az-acr-login] per accedere a un registro. Quando si usa l'interfaccia della riga di comando, l'uso di un certificato come segreto anziché una password fornisce sicurezza aggiuntiva. 

Quando si [Crea un'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli), è possibile creare un certificato autofirmato. In alternativa, aggiungere uno o più certificati a un'entità servizio esistente. Se ad esempio si usa uno degli script in questo articolo per creare o aggiornare un'entità servizio con diritti per eseguire il pull o il push di immagini da un registro, aggiungere un certificato usando il comando [AZ ad SP Credential Reset][az-ad-sp-credential-reset] .

Per usare l'entità servizio con il certificato per [accedere all'interfaccia della](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)riga di comando di Azure, il certificato deve essere in formato PEM e includere la chiave privata. Se il certificato non è nel formato richiesto, usare uno strumento come `openssl` per convertirlo. Quando si esegue [AZ login][az-login] per accedere all'interfaccia della riga di comando usando l'entità servizio, specificare anche l'ID applicazione dell'entità servizio e l'ID del tenant Active Directory. Nell'esempio seguente vengono illustrati questi valori come variabili di ambiente:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Eseguire quindi [AZ ACR login][az-acr-login] per l'autenticazione con il registro di sistema:

```azurecli
az acr login --name myregistry
```

L'interfaccia della riga di comando usa il token creato quando è stato eseguito `az login` per autenticare la sessione con il registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica dell'autenticazione](container-registry-authentication.md) per altri scenari di autenticazione con un registro contenitori di Azure.

* Per un esempio di come usare un insieme di credenziali delle chiavi di Azure per archiviare e recuperare le credenziali dell'entità servizio per un registro contenitori, vedere l'esercitazione per [compilare e distribuire un'immagine del contenitore con le attività di ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
