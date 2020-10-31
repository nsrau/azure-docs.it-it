---
title: Flusso di lavoro attività per gestire il contenuto pubblico del registro
description: Creare un flusso di lavoro automatizzato per le attività Container Registry di Azure per tenere traccia, gestire e utilizzare il contenuto di immagini pubbliche in un registro contenitori di Azure privato.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 261604b66d393723b35b472415b8840b047bc36e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133700"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Come usare e gestire il contenuto pubblico con le attività di Container Registry di Azure

Questo articolo fornisce un flusso di lavoro di esempio in Container Registry di Azure per semplificare la gestione dell'utilizzo e della gestione del contenuto pubblico:

1. Importa le copie locali delle immagini pubbliche dipendenti.
1. Convalidare le immagini pubbliche tramite l'analisi della sicurezza e i test funzionali.
1. Innalzare di livello le immagini ai registri privati per l'utilizzo interno.
1. Attivare gli aggiornamenti delle immagini di base per le applicazioni che dipendono da contenuto pubblico.
1. Usare le [attività di container Registry di Azure](container-registry-tasks-overview.md) per automatizzare questo flusso di lavoro.

Il flusso di lavoro viene riepilogato nell'immagine seguente:

![Utilizzo del flusso di lavoro del contenuto pubblico](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Il flusso di lavoro di importazione gestita consente di gestire le dipendenze dell'organizzazione da elementi gestiti esternamente, ad esempio immagini originate da registri pubblici, tra cui [Hub Docker][docker-hub], [GCR][gcr], [Quay][quay], [GitHub container Registry][ghcr], [Microsoft container Registry][mcr]o anche altri [registri contenitori di Azure][acr]. 

Per informazioni di base sui rischi introdotti dalle dipendenze sul contenuto pubblico e su come usare Azure Container Registry per attenuarli, vedere il post di Blog relativo all'utilizzo di [contenuto pubblico OCI][oci-consuming-public-content] e [gestire il contenuto pubblico con container Registry di Azure](buffer-gate-public-content.md).

Per completare questa procedura dettagliata, è possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. È consigliabile usare l'interfaccia della riga di comando di Azure versione 2,10 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-cli].

## <a name="scenario-overview"></a>Panoramica dello scenario

![Importa componenti flusso di lavoro](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Questa procedura dettagliata configura:

1. Tre **registri contenitori** , che rappresentano:
   * Un [Hub Docker][docker-hub] simulato ( `publicregistry` ) per supportare la modifica dell'immagine di base
   * Registro del team ( `contoso` ) per condividere immagini private
   * Registro di sistema condiviso società/team ( `baseartifacts` ) per il contenuto pubblico importato
1. **Attività ACR** in ogni registro di sistema. Attività:  
   1. Creare un'immagine pubblica simulata `node`
   1. Importare e convalidare l' `node` immagine nel registro di sistema condiviso aziendale/team
   1. Compilare e distribuire l' `hello-world` immagine
1. **Definizioni di attività ACR** , incluse le configurazioni per:
1. Raccolta di **credenziali del registro di sistema** , che sono puntatori a Key Vault
1. Raccolta di **segreti** , disponibile all'interno di un `acr-task.yaml` , che sono puntatori a Key Vault
1. Raccolta di **valori configurati** utilizzati all'interno di un oggetto. `acr-task.yaml`
1. **Azure Key Vault** per proteggere tutti i segreti
1. **Istanza di contenitore di Azure** che ospita l' `hello-world` applicazione di compilazione

## <a name="prerequisites"></a>Prerequisiti

Nei passaggi seguenti vengono configurati i valori per le risorse create e utilizzate nella procedura dettagliata.

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Configurare le variabili univoche per l'ambiente in uso. Si seguono le procedure consigliate per inserire le risorse con contenuto durevole nel proprio gruppo di risorse per ridurre al minimo l'eliminazione accidentale. Tuttavia, se lo si desidera, è possibile inserirli in un singolo gruppo di risorse.

Gli esempi in questo articolo sono formattati per la shell bash.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Repository git e token

Per simulare l'ambiente, biforcare ognuno dei repository git seguenti nei repository che è possibile gestire. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Aggiornare quindi le variabili seguenti per i repository con fork.

L' `:main` accodato alla fine degli URL git rappresenta il ramo del repository predefinito.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

È necessario un [token di accesso GitHub (Pat)][git-token] per le attività ACR per clonare e stabilire i webhook git. Per i passaggi relativi alla creazione di un token con le autorizzazioni necessarie per un repository privato, vedere [creare un token di accesso GitHub](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Credenziali dell'hub Docker  
Per evitare richieste di limitazione e di identità durante il pull di immagini dall'hub Docker, creare un [token dell'hub Docker][docker-hub-tokens]. Quindi, impostare le variabili di ambiente seguenti:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Creazione di registri

Usando i comandi dell'interfaccia della riga di comando di Azure, creare tre registri contenitori di livello Premium, ognuno nel relativo gruppo di risorse:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Creare Key Vault e impostare i segreti

Creare un insieme di credenziali delle chiavi:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Impostare il nome utente e il token dell'hub Docker nell'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Impostare e verificare git PAT nell'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Creare un gruppo di risorse per un'istanza di contenitore di Azure

Questo gruppo di risorse viene usato in un'attività successiva quando si distribuisce l' `hello-world` immagine.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Crea `node` immagine di base pubblica

Per simulare l' `node` immagine nell'hub Docker, creare un' [attività ACR][acr-task] per creare e gestire l'immagine pubblica. Questa configurazione consente di simulare le modifiche da parte dei `node` gestori di immagini.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Per evitare la limitazione Docker, aggiungere le [credenziali dell'hub Docker][docker-hub-tokens] all'attività. Il comando [ACR Task Credentials][acr-task-credentials] può essere usato per passare le credenziali di Docker a qualsiasi registro, incluso Docker Hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Concedere all'attività l'accesso per leggere i valori dall'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Le [attività possono essere attivate][acr-task-triggers] da commit Git, aggiornamenti di immagini di base, timer o esecuzioni manuali. 

Eseguire manualmente l'attività per generare l' `node` immagine:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Elencare l'immagine nel registro di sistema pubblico simulato:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Creare l' `hello-world` immagine

Creare un'immagine in base all' `node` immagine pubblica simulata `hello-world` .

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Creare un token per l'accesso pull al registro di sistema pubblico simulato

Creare un [token di accesso][acr-tokens] al registro di sistema pubblico simulato, con ambito `pull` . Quindi, impostarlo nell'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Creare un token per l'accesso pull da istanze di contenitore di Azure

Creare un [token di accesso][acr-tokens] al registro di sistema che ospita l' `hello-world` immagine, con ambito pull. Quindi, impostarlo nell'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Crea attività per compilare e gestire l' `hello-world` immagine

Il comando che segue crea un'attività dalla definizione di `acr-tasks.yaml` nel `hello-world` repository. I passaggi dell'attività compilano l' `hello-world` immagine e la distribuiscono in istanze di contenitore di Azure. Il gruppo di risorse per istanze di contenitore di Azure è stato creato in una sezione precedente. Chiamando `az container create` nell'attività con una sola differenza in `image:tag` , l'attività viene distribuita nella stessa istanza di in questa procedura dettagliata.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Aggiungere le credenziali all'attività per il registro di sistema pubblico simulato:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Concedere all'attività l'accesso per leggere i valori dall'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Concedere all'attività l'accesso per creare e gestire istanze di contenitore di Azure concedendo l'accesso al gruppo di risorse:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Con l'attività creata e configurata, eseguire l'attività per compilare e distribuire l' `hello-world` immagine:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Al termine della creazione, ottenere l'indirizzo IP del contenitore che ospita l' `hello-world` immagine.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Nel browser passare all'indirizzo IP per visualizzare l'applicazione in esecuzione.

## <a name="update-the-base-image-with-a-questionable-change"></a>Aggiornare l'immagine di base con una modifica "discutibile"

In questa sezione viene simulata una modifica all'immagine di base che può causare problemi nell'ambiente.

1. Aprire `Dockerfile` nel repository con fork `base-image-node` .
1. Modificare `BACKGROUND_COLOR` in `Orange` per simulare la modifica.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Eseguire il commit della modifica e controllare le attività ACR per avviare automaticamente la compilazione.

Controllare l'avvio dell'esecuzione dell'attività:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Verrà visualizzato lo stato `Succeeded` in base a un trigger di `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Digitare **CTRL + C** per uscire dal comando Watch, quindi visualizzare i log per l'esecuzione più recente:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Una volta `node` completata l'immagine, `watch` per le attività ACR per iniziare automaticamente a compilare l' `hello-world` immagine:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Verrà visualizzato lo stato `Succeeded` in base a un trigger di `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Digitare **CTRL + C** per uscire dal comando Watch, quindi visualizzare i log per l'esecuzione più recente:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Al termine, ottenere l'indirizzo IP del sito che ospita l' `hello-world` immagine aggiornata:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Nel browser passare al sito, che dovrebbe avere uno sfondo arancione (discutibile).

### <a name="checking-in"></a>Archiviazione in

A questo punto, è stata creata un' `hello-world` immagine che viene compilata automaticamente in base ai commit Git e alle modifiche apportate all'immagine di base `node` . In questo esempio, l'attività si basa su un'immagine di base in Azure Container Registry, ma è possibile usare qualsiasi registro di sistema supportato.

L'aggiornamento dell'immagine di base riattiva automaticamente l'esecuzione dell'attività durante l' `node` aggiornamento dell'immagine. Come illustrato qui, non tutti gli aggiornamenti sono desiderati.

## <a name="gated-imports-of-public-content"></a>Importazioni gestite del contenuto pubblico

Per evitare modifiche upstream dai carichi di lavoro critici di rilievo, è possibile che vengano aggiunte analisi di sicurezza e test funzionali.

In questa sezione viene creata un'attività ACR per:

* Creare un'immagine di test
* Eseguire uno script di test funzionale sull' `./test.sh` immagine di test
* Se l'immagine viene verificata correttamente, importare l'immagine pubblica nel registro di sistema **BaseImages**

### <a name="add-automation-testing"></a>Aggiungi test di automazione

Per controllare qualsiasi contenuto upstream, viene implementato il test automatizzato. In questo esempio `test.sh` viene fornito un oggetto che controlla `$BACKGROUND_COLOR` . Se il test ha esito negativo, `EXIT_CODE` `1` viene restituito un di che causa l'esito negativo del passaggio dell'attività ACR, che termina l'esecuzione dell'attività. È possibile espandere i test in qualsiasi forma di strumenti, inclusi i risultati della registrazione. Il controllo viene gestito da una risposta con esito negativo nello script, riprodotto qui:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>YAML attività 

Esaminare il `acr-task.yaml` nel `import-baseimage-node` repository, che esegue i passaggi seguenti:

1. Compilare l'immagine di base del test usando il Dockerfile seguente:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Al termine, convalidare l'immagine eseguendo il contenitore, che esegue `./test.sh`
1. Solo se completato correttamente, eseguire i passaggi di importazione, che vengono controllati con `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Crea attività per importare e testare un'immagine di base

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Aggiungere le credenziali all'attività per il registro di sistema pubblico simulato:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Concedere all'attività l'accesso per leggere i valori dall'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Eseguire l'attività di importazione:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Se l'attività ha esito negativo a causa di `./test.sh: Permission denied` , assicurarsi che lo script disponga delle autorizzazioni di esecuzione ed eseguire il commit nel repository git:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Aggiornare l' `hello-world` immagine da compilare da un' `node` immagine gestita

Creare un [token di accesso][acr-tokens] per accedere al registro elementi di base, con ambito `read` dal `node` repository. Quindi, impostare nell'insieme di credenziali delle chiavi:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Aggiungere le credenziali all'attività **Hello-World** per il registro elementi di base:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Aggiornare l'attività per modificare l' `REGISTRY_FROM_URL` utilizzo del `BASE_ARTIFACTS` Registro di sistema

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Eseguire l'attività **Hello-World** per modificare la dipendenza dell'immagine di base:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Aggiornare l'immagine di base con una modifica "valida"

1. Aprire `Dockerfile` nel `base-image-node` repository.
1. Modificare `BACKGROUND_COLOR` in `Green` per simulare una modifica valida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Eseguire il commit della modifica e monitorare la sequenza di aggiornamenti:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Una volta eseguito, digitare **CTRL + C** e monitorare i log:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Al termine, monitorare l'attività **di importazione-immagine di base** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Una volta eseguito, digitare **CTRL + C** e monitorare i log:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Al termine, monitorare l'attività **Hello-World** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Una volta eseguito, digitare **CTRL + C** e monitorare i log:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Al termine, ottenere l'indirizzo IP del sito che ospita l' `hello-world` immagine aggiornata:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Nel browser passare al sito, che dovrebbe avere uno sfondo verde (valido).

### <a name="view-the-gated-workflow"></a>Visualizzare il flusso di lavoro controllato

Eseguire di nuovo i passaggi nella sezione precedente, con un colore di sfondo rosso.

1. Aprire `Dockerfile` nel `base-image-node` repository
1. Modificare `BACKGROUND_COLOR` in `Red` per simulare una modifica non valida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Eseguire il commit della modifica e monitorare la sequenza di aggiornamenti:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Una volta eseguito, digitare **CTRL + C** e monitorare i log:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Al termine, monitorare l'attività **di importazione-immagine di base** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Una volta eseguito, digitare **CTRL + C** e monitorare i log:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

A questo punto, si noterà che l'attività di **base-importazione-nodo** ha esito negativo e arresta la sequenza per pubblicare un `hello-world` aggiornamento. L'output è simile a:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Pubblicare un aggiornamento in `hello-world`

Le modifiche apportate all' `hello-world` immagine continueranno a utilizzare l'ultima immagine convalidata `node` .

Eventuali altre modifiche apportate all'immagine di base `node` che passano le convalide gestite attiverà gli aggiornamenti dell'immagine di base all' `hello-world` immagine.

## <a name="cleaning-up"></a>Pulizia

Quando non è più necessario, eliminare le risorse usate in questo articolo.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo. sono state usate le attività ACR per creare un flusso di lavoro di controllo automatizzato per introdurre le immagini di base aggiornate nell'ambiente. Vedere le informazioni correlate per gestire le immagini in Azure Container Registry.


* [Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md)
* [Bloccare un'immagine del contenitore in un registro contenitori di Azure](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         https://aka.ms/acr/repo-permissions
[acr-task]:                     https://aka.ms/acr/tasks
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   https://aka.ms/acr/tokens
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                https://aka.ms/acr/artifacts
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io




