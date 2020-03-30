---
title: Autenticazione esterna dall'attività ACR
description: Configurare un'attività del Registro di sistema del contenitore di Azure (attività ACR) per leggere le credenziali dell'hub Docker archiviate in un insieme di credenziali delle chiavi di Azure usando un'identità gestita per le risorse di Azure.Configure an Azure Container Registry Task (ACR Task) to read Docker Hub credentials stored in an Azure key vault, by using a managed identity for Azure resources.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842521"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticazione esterna in un'attività ACR usando un'identità gestita da AzureExternal authentication in an ACR task using an Azure-managed identity 

In [un'attività ACR](container-registry-tasks-overview.md)è possibile [abilitare un'identità gestita per](container-registry-tasks-authentication-managed-identity.md)le risorse di Azure. L'attività può usare l'identità per accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. 

In questo articolo viene illustrato come abilitare un'identità gestita in un'attività che accede ai segreti archiviati in un insieme di credenziali delle chiavi di Azure.In this article, you learn how to enable a managed identity in a task that accesses secrets stored in an Azure key vault. 

Per creare le risorse di Azure, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="scenario-overview"></a>Panoramica dello scenario

L'attività di esempio legge le credenziali docker Hub archiviate in un insieme di credenziali delle chiavi di Azure.The example task reads Docker Hub credentials stored in an Azure key vault. Le credenziali sono per un account Docker Hub con autorizzazioni di scrittura (push) per un repository Docker Hub privato. Per leggere le credenziali, configurare l'attività con un'identità gestita e assegnarle le autorizzazioni appropriate. L'attività associata all'identità crea un'immagine e accede a Docker Hub per eseguire il push dell'immagine nel repository privato. 

In questo esempio viene illustrata la procedura di utilizzo di un'identità gestita assegnata dall'utente o dal sistema. La scelta dell'identità dipende dalle esigenze dell'organizzazione.

In uno scenario reale, un'azienda potrebbe pubblicare immagini in un repository privato in Docker Hub come parte di un processo di compilazione. 

## <a name="prerequisites"></a>Prerequisiti

È necessario un registro contenitori di Azure in cui si esegue l'attività. In questo articolo, questo Registro di sistema è denominato *myregistry*. Sostituire con il proprio nome del Registro di sistema nei passaggi successivi.

Se non si dispone già di un registro dei contenitori di Azure, vedere [Guida introduttiva: Creare un registro di contenitori privati usando l'interfaccia della riga di comando](container-registry-get-started-azure-cli.md)di Azure.If you don't already have an Azure container registry, see Quickstart: Create a private container registry using the Azure CLI . Non è ancora necessario eseguire il push delle immagini nel Registro di sistema.

È inoltre necessario un repository privato in Docker Hub e un account Docker Hub con le autorizzazioni per scrivere nel repository. In questo esempio, questo repository è denominato *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Creare un insieme di credenziali delle chiavi e archiviare i segretiCreate a key vault and store secrets

In primo luogo, se necessario, creare un gruppo di risorse denominato myResourceGroup nel percorso eastus con il comando az group create seguente:First, if you need, create a resource group named *myResourceGroup* in the *eastus* location with the following [az group create][az-group-create] command:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilizzare il comando [az keyvault create][az-keyvault-create] per creare un insieme di credenziali delle chiavi. Assicurarsi di specificare un nome univoco dell'insieme di credenziali delle chiavi. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Archiviare le credenziali Docker Hub necessarie nell'insieme di credenziali delle chiavi usando il comando [az keyvault secret set.][az-keyvault-secret-set] In questi comandi, i valori vengono passati nelle variabili di ambiente:In these commands, the values are passed in environment variables:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

In uno scenario reale, i segreti verrebbero probabilmente impostati e gestiti in un processo separato.

## <a name="define-task-steps-in-yaml-file"></a>Definire i passaggi delle attività nel file YAMLDefine task steps in YAML file

I passaggi per questa attività di esempio sono definiti in un [file YAML](container-registry-tasks-reference-yaml.md). Creare un `dockerhubtask.yaml` file denominato in una directory di lavoro locale e incollare il contenuto seguente. Assicurarsi di sostituire il nome dell'insieme di credenziali delle chiavi nel file con il nome dell'insieme di credenziali delle chiavi.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

I passaggi dell'attività eseguire le operazioni seguenti:The task steps do the following:

* Gestire le credenziali segrete per l'autenticazione con Docker Hub.Manage secret credentials to authenticate with Docker Hub.
* Eseguire l'autenticazione con Docker `docker login` Hub passando i segreti al comando.
* Creare un'immagine usando un dockerfile di esempio nel repository [Azure-Samples/acr-tasks.Build](https://github.com/Azure-Samples/acr-tasks.git) an image using a sample Dockerfile in the Azure-Samples/acr-tasks repo.
* Eseguire il push dell'immagine nel repository Docker Hub privato.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opzione 1: Creare un'attività con l'identità assegnata dall'utenteOption 1: Create task with user-assigned identity

I passaggi descritti in questa sezione consentono di creare un'attività e abilitare un'identità assegnata dall'utente. Se invece si desidera abilitare un'identità assegnata dal sistema, vedere [Opzione 2: Creare un'attività con identità assegnata dal sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Creare un'attività

Creare l'attività *dockerhubtask* eseguendo il comando [az acr task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto `dockerhubtask.yaml` del codice sorgente e il comando fa riferimento al file nella directory di lavoro. Il `--assign-identity` parametro passa l'ID risorsa dell'identità assegnata dall'utente. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opzione 2: Creare un'attività con un'identità assegnata dal sistemaOption 2: Create task with system-assigned identity

I passaggi in questa sezione creano un'attività e abilitano un'identità assegnata dal sistema. Se invece si desidera abilitare un'identità assegnata dall'utente, vedere [Opzione 1: Creare un'attività con identità assegnata dall'utente](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Creare un'attività

Creare l'attività *dockerhubtask* eseguendo il comando [az acr task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto `dockerhubtask.yaml` del codice sorgente e il comando fa riferimento al file nella directory di lavoro. Il `--assign-identity` parametro senza valore abilita l'identità assegnata dal sistema nell'attività.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Concedere l'accesso di identità all'insieme di credenziali delle chiaviGrant identity access to key vault

Eseguire il comando [az keyvault set-policy][az-keyvault-set-policy] seguente per impostare un criterio di accesso all'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità di leggere i segreti dall'insieme di credenziali delle chiavi. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Eseguire manualmente l'attività

Per verificare che l'attività in cui è stata abilitata un'identità gestita venga eseguita correttamente, attivare manualmente l'attività con il comando [az acr task run.][az-acr-task-run] Il `--set` parametro viene utilizzato per passare il nome del repository privato all'attività. In questo esempio, il nome del repository segnaposto è *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Quando l'attività viene eseguita correttamente, l'output mostra la corretta autenticazione a Docker Hub e l'immagine viene compilata e inviata al repository privato:When the task runs successfully, output shows successful authentication to Docker Hub, and the image is successfully built and pushed to the private repo:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Per verificare che l'immagine venga`cf24` inserita, verificare la presenza del tag (in questo esempio) nel repository docker Hub privato.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'abilitazione di un'identità gestita in un'attività ACR](container-registry-tasks-authentication-managed-identity.md).
* Vedere le informazioni di riferimento su [Attività ACR YAML](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
