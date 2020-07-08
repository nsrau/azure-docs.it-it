---
title: Autenticazione esterna dall'attività ACR
description: Configurare un'attività Container Registry di Azure (attività ACR) per leggere le credenziali dell'hub Docker archiviate in un insieme di credenziali delle chiavi di Azure usando un'identità gestita per le risorse di Azure.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058130"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticazione esterna in un'attività ACR usando un'identità gestita da Azure 

In un'[attività del Registro Azure Container](container-registry-tasks-overview.md) è possibile [abilitare un'identità gestita per le risorse di Azure](container-registry-tasks-authentication-managed-identity.md). L'attività può usare l'identità per accedere ad altre risorse di Azure, senza la necessità di specificare o gestire credenziali. 

Questo articolo illustra come abilitare un'identità gestita in un'attività che accede ai segreti archiviati in un insieme di credenziali delle chiavi di Azure. 

Per creare le risorse di Azure, questo articolo richiede che venga eseguita l'interfaccia della riga di comando di Azure versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="scenario-overview"></a>Panoramica dello scenario

L'attività di esempio legge le credenziali dell'hub Docker archiviate in Azure Key Vault. Le credenziali si rimandano a un account Docker Hub con autorizzazioni di scrittura (push) per un repository Docker Hub privato. Per leggere le credenziali, configurare l'attività con un'identità gestita e assegnarvi le autorizzazioni appropriate. L'attività associata all'identità compila un'immagine e accede all'hub Docker per eseguire il push dell'immagine nel repository privato. 

Questo esempio illustra i passaggi con un'identità gestita assegnata dall'utente o dal sistema. La scelta dell'identità dipende dalle esigenze della propria organizzazione.

In uno scenario reale, un'azienda potrebbe pubblicare immagini in un repository privato nell'hub Docker come parte di un processo di compilazione. 

## <a name="prerequisites"></a>Prerequisiti

È necessario un registro contenitori di Azure in cui eseguire l'attività. In questo articolo questo registro è denominato *myregistry*. Sostituire con il nome del registro di sistema nei passaggi successivi.

Se non si ha già un registro contenitori di Azure, vedere [Guida introduttiva: creare un registro contenitori privato usando l'interfaccia della](container-registry-get-started-azure-cli.md)riga di comando di Azure. Non è ancora necessario eseguire il push delle immagini nel registro.

È necessario anche un repository privato nell'hub Docker e un account Docker Hub con le autorizzazioni per la scrittura nel repository. In questo esempio, il repository è denominato *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Creare un insieme di credenziali delle chiavi e archiviare i segreti

Prima di tutto, se necessario, creare un gruppo di risorse denominato *myResourceGroup* nella località *eastus* con il comando [AZ Group create][az-group-create] seguente:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare il comando [AZ Key Vault create][az-keyvault-create] per creare un insieme di credenziali delle chiavi. Assicurarsi di specificare un nome univoco dell'insieme di credenziali delle chiavi. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Archiviare le credenziali dell'hub Docker necessarie nell'insieme di credenziali delle chiavi usando il comando [AZ Keys Vault Secret set][az-keyvault-secret-set] . In questi comandi, i valori vengono passati nelle variabili di ambiente:

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

In uno scenario reale, è probabile che i segreti vengano impostati e mantenuti in un processo separato.

## <a name="define-task-steps-in-yaml-file"></a>Definire i passaggi dell'attività nel file YAML

I passaggi per questa attività di esempio sono definiti in un [file YAML](container-registry-tasks-reference-yaml.md). Creare un file denominato `dockerhubtask.yaml` in una directory di lavoro locale e incollare il contenuto seguente. Assicurarsi di sostituire il nome dell'insieme di credenziali delle chiavi nel file con il nome dell'insieme di credenziali delle chiavi.

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

I passaggi dell'attività eseguono le operazioni seguenti:

* Gestire le credenziali segrete per l'autenticazione con Docker Hub.
* Eseguire l'autenticazione con Docker Hub passando i segreti al `docker login` comando.
* Compilare un'immagine usando un Dockerfile di esempio nel repository [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) .
* Eseguire il push dell'immagine nel repository dell'hub Docker privato.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opzione 1: Creare un'attività con un'identità assegnata dall'utente

I passaggi di questa sezione creano un'attività e abilitano un'identità assegnata dall'utente. Per abilitare invece un'identità assegnata dal sistema, vedere [Opzione 2: Creare un'attività con un'identità assegnata dal sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Creare un'attività

Creare l'attività *dockerhubtask* eseguendo il comando [AZ ACR task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto del codice sorgente e il comando fa riferimento al file `dockerhubtask.yaml` nella directory di lavoro. Il parametro `--assign-identity` passa l'ID risorsa dell'identità assegnata dall'utente. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Concedere l'accesso all'identità a Key Vault

Eseguire il comando [AZ Key Vault set-Policy][az-keyvault-set-policy] seguente per impostare un criterio di accesso nell'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità di leggere i segreti dall'insieme di credenziali delle chiavi. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Passare a [eseguire manualmente l'attività](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opzione 2: Creare un'attività con un'identità assegnata dal sistema

I passaggi di questa sezione creano un'attività e abilitano un'identità assegnata dal sistema. Per abilitare invece un'identità assegnata dall'utente, vedere [Opzione 1: Creare un'attività con un'identità assegnata dall'utente](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Creare un'attività

Creare l'attività *dockerhubtask* eseguendo il comando [AZ ACR task create][az-acr-task-create] seguente. L'attività viene eseguita senza un contesto del codice sorgente e il comando fa riferimento al file `dockerhubtask.yaml` nella directory di lavoro. Il parametro `--assign-identity` senza valori abilita l'identità gestita assegnata dal sistema nell'attività.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Concedere l'accesso all'identità a Key Vault

Eseguire il comando [AZ Key Vault set-Policy][az-keyvault-set-policy] seguente per impostare un criterio di accesso nell'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità di leggere i segreti dall'insieme di credenziali delle chiavi. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Eseguire manualmente l'attività

Per verificare che l'attività in cui è stata abilitata un'identità gestita venga eseguita correttamente, attivarla manualmente con il comando [az acr task run][az-acr-task-run]. Il `--set` parametro viene usato per passare il nome del repository privato all'attività. In questo esempio il nome del repository segnaposto è *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Quando l'attività viene eseguita correttamente, l'output Mostra l'autenticazione corretta nell'hub Docker e l'immagine viene compilata e inserita correttamente nel repository privato:

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

Per confermare che l'immagine viene inserita, cercare il tag ( `cf24` in questo esempio) nel repository dell'hub Docker privato.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'[abilitazione di un'identità gestita in un'attività del Registro Azure Container](container-registry-tasks-authentication-managed-identity.md).
* Vedere le [informazioni di riferimento sul file YAML delle attività del Registro Azure Container](container-registry-tasks-reference-yaml.md)


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
