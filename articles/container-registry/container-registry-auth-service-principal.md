---
title: Eseguire l'autenticazione con l'entità servizio
description: Consentire l'accesso alle immagini del registro contenitori privato usando un'entità servizio Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455400"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticazione al Registro Azure Container con entità servizio

È possibile usare un'entità servizio Azure Active Directory (Azure AD) per fornire l'accesso `docker push` e `pull` dell'immagine del contenitore al registro contenitori. Se si usa un'entità servizio, è possibile fornire l'accesso ai servizi e alle applicazioni "headless".

## <a name="what-is-a-service-principal"></a>Che cos'è un'entità servizio?

Le *entità servizio* di Azure AD forniscono accesso alle risorse Azure all'interno della sottoscrizione. È possibile considerare un'entità servizio come un'identità utente per un servizio, dove "service" è qualsiasi applicazione, servizio o piattaforma che deve accedere alle risorse. È possibile configurare un'entità servizio con diritti di accesso limitati solo alle risorse specificate. Configurare quindi l'applicazione o il servizio in modo che usi le credenziali dell'entità servizio per accedere a tali risorse.

Nel contesto del Registro Azure Container è possibile creare un'entità servizio Azure AD con autorizzazioni pull, push e pull o con altre autorizzazioni per il registro Docker privato in Azure. Per l'elenco completo, vedere [Azure Container Registry roles and permissions](container-registry-roles.md) (Ruoli e autorizzazioni del Registro Azure Container).

## <a name="why-use-a-service-principal"></a>Perché usare un'entità servizio?

Usando un'entità servizio Azure AD, è possibile fornire un accesso limitato al registro di sistema del contenitore privato. Creare entità servizio diverse per ogni applicazione o servizio, ognuno con diritti di accesso personalizzati al Registro di sistema. Poiché è possibile evitare di condividere le credenziali tra servizi e applicazioni, è possibile ruotare le credenziali o revocare l'accesso solamente all'entità servizio (e quindi all'applicazione) scelta.

Ad esempio, configurare l'applicazione Web per l'utilizzo di un'entità servizio che le fornisca solo l'accesso all'immagine, `pull` mentre il sistema di compilazione usa un'entità servizio che le fornisce sia `push` `pull` l'accesso a entrambi che. Se lo sviluppo dell'applicazione passa di mano, è possibile ruotare le credenziali dell'entità servizio senza influire sul sistema di compilazione.

## <a name="when-to-use-a-service-principal"></a>Quando usare un'entità servizio

Usare un'entità servizio per fornire accesso al registro negli **scenari headless**, ovvero per qualsiasi applicazione, servizio o script che deve eseguire le operazioni di push o pull delle immagini del contenitore in modo automatico. Ad esempio:

  * *Pull*: Distribuire contenitori da un Registro di sistema ai sistemi di orchestrazione, tra cui Kubernetes, DC/OS e Docker Swarm. È anche possibile eseguire il pull dai registri dei contenitori ai servizi di Azure correlati, ad esempio il [servizio Azure Kubernetes (AKS),](../aks/cluster-container-registry-integration.md)le [istanze del contenitore di Azure](container-registry-auth-aci.md), il servizio [app,](../app-service/index.yml) [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e altri.

  * *Push:* creare immagini del contenitore ed eseguirne il push in un Registro di sistema usando soluzioni di integrazione e distribuzione continue come Azure Pipelines o Jenkins.Push : Build container images and push them to a registry using continuous integration and deployment solutions like Azure Pipelines or Jenkins.

Per l'accesso individuale a un Registro di sistema, ad esempio quando si esegue manualmente il pull di un'immagine del contenitore nella workstation di sviluppo, è consigliabile usare la propria [identità](container-registry-authentication.md#individual-login-with-azure-ad) di Azure AD per l'accesso al Registro di sistema (ad esempio, con [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Script di esempio

È possibile trovare gli script di esempio precedenti per l'interfaccia della riga di comando di Azure in GitHub, nonché le versioni per Azure PowerShell:You can find the preceding sample scripts for Azure CLI on GitHub, as well as versions for Azure PowerShell:

* [Interfaccia della riga di comando di AzureAzure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Eseguire l'autenticazione con l'entità servizioAuthenticate with the service principal

Dopo aver concesso l'accesso al Registro di sistema del contenitore, è possibile configurare le credenziali per l'accesso a servizi e applicazioni "headless" oppure immetterle utilizzando il `docker login` comando. Usare i valori seguenti:

* **Nome utente** - ID applicazione entità servizio (denominato anche *ID client)*
* **Password** - password dell'entità servizio (denominata anche *segreto client)*

Ogni valore è un `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID del modulo . 

> [!TIP]
> È possibile rigenerare la password di un'entità servizio eseguendo il comando [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset).
>

### <a name="use-credentials-with-azure-services"></a>Usare le credenziali con i servizi di AzureUse credentials with Azure services

È possibile usare le credenziali dell'entità servizio da qualsiasi servizio di Azure che esegue l'autenticazione con un registro contenitori di Azure.You can use service principal credentials from any Azure service that authenticates with an Azure container registry.  Usare le credenziali dell'entità servizio al posto delle credenziali di amministratore del Registro di sistema per un'ampia gamma di scenari.

Ad esempio, usare le credenziali per estrarre un'immagine da un registro contenitori di Azure a [Istanze del contenitore](container-registry-auth-aci.md)di Azure.For example, use the credentials to pull an image from an Azure container registry to Azure Container Instances.

### <a name="use-with-docker-login"></a>Utilizzare con docker login

È possibile `docker login` eseguire usando un'entità servizio. Nell'esempio seguente, l'ID applicazione dell'entità `$SP_APP_ID`servizio viene passato `$SP_PASSWD`nella variabile di ambiente e la password nella variabile . Per le procedure consigliate per gestire le credenziali Docker, vedere le informazioni di riferimento sui comandi di [accesso docker.For](https://docs.docker.com/engine/reference/commandline/login/) best practices to manage Docker credentials, see the docker login command reference.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Una volta effettuato l'accesso, Docker memorizza nella cache le credenziali.

### <a name="use-with-certificate"></a>Utilizzo con certificato

Se è stato aggiunto un certificato all'entità servizio, è possibile accedere all'interfaccia della riga di comando di Azure con l'autenticazione basata su certificati e quindi usare il comando [az acr login][az-acr-login] per accedere a un Registro di sistema. L'utilizzo di un certificato come segreto anziché di una password fornisce ulteriore sicurezza quando si utilizza l'interfaccia della riga di comando. 

Quando si [crea un'entità servizio,](/cli/azure/create-an-azure-service-principal-azure-cli)è possibile creare un certificato autofirmato. In alternativa, aggiungere uno o più certificati a un'entità servizio esistente. Ad esempio, se si usa uno degli script in questo articolo per creare o aggiornare un'entità servizio con diritti per il pull o il push di immagini da un Registro di sistema, aggiungere un certificato usando il comando az ad sp per la [reimpostazione delle credenziali.][az-ad-sp-credential-reset]

Per usare l'entità servizio con certificato per [accedere all'interfaccia della riga](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)di comando di Azure, il certificato deve essere in formato PEM e includere la chiave privata. Se il certificato non è nel formato richiesto, `openssl` utilizzare uno strumento come per convertirlo. Quando si esegue [az login][az-login] per accedere alla CLI utilizzando l'entità servizio, fornire anche l'ID applicazione dell'entità servizio e l'ID tenant di Active Directory. L'esempio seguente mostra questi valori come variabili di ambiente:The following example shows these values as environment variables:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Quindi, eseguire [az acr login][az-acr-login] per l'autenticazione con il Registro di sistema:

```azurecli
az acr login --name myregistry
```

L'interfaccia della riga di `az login` comando utilizza il token creato durante l'esecuzione per autenticare la sessione con il Registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

* Vedere la [panoramica dell'autenticazione](container-registry-authentication.md) per altri scenari per l'autenticazione con un registro contenitori di Azure.See the authentication overview for other scenarios to authenticate with an Azure container registry.

* Per un esempio di utilizzo di un insieme di credenziali delle chiavi di Azure per archiviare e recuperare le credenziali dell'entità servizio per un registro contenitori, vedere l'esercitazione per [compilare e distribuire un'immagine del contenitore usando attività ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
