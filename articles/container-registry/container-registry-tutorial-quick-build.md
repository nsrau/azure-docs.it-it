---
title: Esercitazione - Compilare immagini dei contenitori nel cloud con ACR Build
description: Questa esercitazione illustra come compilare un'immagine del contenitore Docker in Azure con la funzionalità di compilazione di Registro contenitori di Azure (ACR Build) e quindi distribuire tale immagine in Istanze di contenitore di Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Esercitazione: Compilare immagini dei contenitori nel cloud con ACR Build

**ACR Build** è un gruppo di funzionalità in Registro contenitori di Azure che consente una compilazione efficiente e semplificata delle immagini dei contenitori Docker. Questo articolo illustra come usare la funzionalità di *compilazione rapida* di ACR Build. La compilazione rapida estende il "ciclo interno" di sviluppo al cloud, con convalida del completamento della compilazione e push automatico delle immagini compilate correttamente nel registro contenitori. Le immagini vengono compilate in modo nativo nel cloud, in prossimità del registro, in modo da velocizzare la distribuzione.

Tutte le competenze acquisite nei documenti Dockerfile sono trasferibili direttamente ad ACR Build. Per la compilazione nel cloud con ACR Build non è necessario modificare i documenti Dockerfile, ma solo il comando che viene eseguito.

In questa esercitazione, che è la prima parte di una serie, si apprenderà come:

> [!div class="checklist"]
> * Ottenere il codice sorgente dell'applicazione di esempio
> * Compilare un'immagine del contenitore in Azure
> * Distribuire un contenitore in Istanze di contenitore di Azure

Le esercitazioni successive illustrano come usare le attività di compilazione di ACR Build per automatizzare la compilazione delle immagini dei contenitori in caso di commit del codice e di aggiornamento delle immagini di base.

> [!IMPORTANT]
> ACR Build è attualmente in anteprima ed è supportato dai registri contenitori di Azure solo nelle aree **Stati Uniti orientali** (eastus) ed **Europa occidentale** (westeurope). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata l'interfaccia della riga di comando di Azure versione **2.0.32** o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli].

## <a name="prerequisites"></a>Prerequisiti

### <a name="github-account"></a>Account GitHub

Se non se ne ha già uno, creare un account in https://github.com. In questa serie di esercitazioni viene usato un repository GitHub per illustrare la compilazione automatica di immagini in ACR Build.

### <a name="fork-sample-repository"></a>Creare una copia tramite fork del repository di esempio

Usare quindi l'interfaccia utente di GitHub per creare una copia tramite fork del repository di esempio nell'account GitHub. In questa esercitazione si compila un'immagine del contenitore dall'origine nel repository, mentre nell'esercitazione successiva si esegue il push di un commit nel fork del repository per avviare una compilazione automatica.

Creare una copia tramite fork del repository https://github.com/Azure-Samples/acr-build-helloworld-node

![Screenshot del pulsante Fork (evidenziato) in GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Clonare il fork

Dopo aver creato una copia tramite fork del repository, clonare il fork e passare alla directory contenente il clone locale.

Clonare il repository con `git`, sostituendo **\<your-github-username\>** con il proprio nome utente GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Passare alla directory contenente il codice sorgente:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Shell Bash

I comandi in questa serie di esercitazioni sono presentati nel formato per la shell Bash. Se si preferisce usare PowerShell, il prompt dei comandi o un'altra shell, potrebbe essere necessario modificare di conseguenza il formato di continuazione di riga e delle variabili di ambiente.

## <a name="build-in-azure-with-acr-build"></a>Compilare in Azure con ACR Build

Dopo aver eseguito il pull del codice sorgente nel computer in uso, seguire questa procedura per creare un registro contenitori e compilare l'immagine del contenitore con ACR Build.

Per semplificare l'esecuzione dei comandi di esempio, nelle esercitazioni di questa serie vengono usate variabili di ambiente della shell. Eseguire il comando seguente per impostare la variabile `ACR_NAME`. Sostituire **\<registry-name\>** con un nome univoco per il nuovo registro contenitori. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Dato che le altre risorse create in questa esercitazione sono basate su questo nome, sarà necessario modificare solo questa prima variabile.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Dopo aver popolato la variabile di ambiente del registro contenitori, sarà possibile copiare e incollare il resto dei comandi dell'esercitazione senza modificare alcun valore. Eseguire questi comandi per creare un gruppo di risorse e un registro contenitori:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Ora che è disponibile un registro, usare ACR Build per compilare un'immagine del contenitore dal codice di esempio. Eseguire il comando [az acr build][az-acr-build] per eseguire una *compilazione rapida*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

L'output del comando [az acr build][az-acr-build] è simile al seguente. Vengono visualizzati il caricamento del codice sorgente ("context") in Azure e i dettagli dell'operazione `docker build` eseguita da ACR Build nel cloud. Dato che ACR Build usa `docker build` per compilare le immagini, è possibile iniziare a usare subito ACR Build senza apportare alcuna modifica ai documenti Dockerfile.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

Nella parte finale dell'output, ACR Build mostra le dipendenze individuate per l'immagine. ACR Build può così automatizzare la compilazione di immagini in caso di aggiornamenti dell'immagine di base, ad esempio quando un'immagine di base viene aggiornata con patch del sistema operativo o del framework. Il supporto di ACR Build per gli aggiornamenti delle immagini di base verrà illustrato più avanti in questa serie di esercitazioni.

## <a name="deploy-to-azure-container-instances"></a>Eseguire la distribuzione in Istanze di contenitore di Azure

Per impostazione predefinita, ACR Build esegue automaticamente il push delle immagini compilate correttamente nel registro, dal quale possono essere distribuite immediatamente.

In questa sezione si creano un'istanza di Azure Key Vault e un'entità servizio e quindi si distribuisce il contenitore in Istanze di contenitore di Azure usando le credenziali dell'entità servizio.

### <a name="configure-registry-authentication"></a>Configurare l'autenticazione del registro

In tutti gli scenari di produzione dovrebbero essere usate [entità servizio][service-principal-auth] per accedere a un registro contenitori di Azure. Mediante le entità servizio è possibile fornire il controllo degli accessi in base al ruolo alle immagini del contenitore. Ad esempio, è possibile configurare un'entità servizio con accesso pull-only a un registro.

#### <a name="create-key-vault"></a>Creare un insieme di credenziali delle chiavi

Se non si ha già un insieme di credenziali delle chiavi in [Azure Key Vault](/azure/key-vault/), crearne uno usando i comandi seguenti nell'interfaccia della riga di comando di Azure.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Creare un'entità servizio e archiviare le credenziali

A questo punto occorre creare un'entità servizio e archiviarne le credenziali nell'insieme di credenziali delle chiavi.

Usare il comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per creare l'entità servizio e [az keyvault secret set][az-keyvault-secret-set] per archiviare la **password** dell'entità servizio nell'insieme di credenziali:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

L'argomento `--role` nel comando precedente configura l'entità servizio con il ruolo *lettore*, che concede l'accesso di tipo pull-only al registro. Per concedere l'accesso sia push che pull, impostare l'argomento `--role` su *collaboratore*.

Archiviare quindi nell'insieme di credenziali il valore *appId* dell'entità servizio, che è il **nome utente** passato a Registro contenitori di Azure per l'autenticazione:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

È stato creato un insieme di credenziali delle chiavi di Azure e vi sono stati archiviati due segreti:

* `$ACR_NAME-pull-usr`: ID dell'entità servizio, da usare come **nome utente** del registro contenitori.
* `$ACR_NAME-pull-pwd`: password dell'entità servizio, da usare come **password** del registro contenitori.

Ora è possibile fare riferimento a questi segreti per nome quando gli utenti o le applicazioni e i servizi eseguono il pull di immagini dal registro.

### <a name="deploy-container-with-azure-cli"></a>Distribuire un contenitore con l'interfaccia della riga di comando di Azure

Ora che le credenziali dell'entità servizio sono archiviate come segreti di Azure Key Vault, le applicazioni e i servizi possono usarle per accedere al registro privato.

Eseguire il comando [az container create][az-container-create] seguente per distribuire un'istanza di contenitore. Il comando usa le credenziali dell'entità servizio archiviate in Azure Key Vault per eseguire l'autenticazione al registro contenitori.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Dato che il valore di `--dns-name-label` deve essere univoco in Azure, il comando precedente aggiunge il nome del registro contenitori all'etichetta del nome DNS del contenitore. L'output del comando visualizza il nome di dominio completo (FQDN) del contenitore, ad esempio:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Prendere nota del nome di dominio completo del contenitore, perché verrà usato nella sezione successiva.

### <a name="verify-deployment"></a>Verificare la distribuzione

Per controllare il processo di avvio del contenitore, usare il comando [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

L'output di `az container attach` mostra per prima cosa lo stato del contenitore durante il pull dell'immagine e l'avvio, quindi associa STDOUT e STDERR della console locale a STDOUT e STDERR del contenitore.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Quando viene visualizzato `Server running at http://localhost:80`, passare al nome di dominio completo del contenitore per visualizzare l'applicazione in esecuzione:

![Screenshot del rendering dell'applicazione di esempio nel browser][quick-build-02-browser]

Per scollegare la console dal contenitore, premere `Control+C`.

## <a name="clean-up-resources"></a>Pulire le risorse

Arrestare l'istanza del contenitore con il comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Per rimuovere *tutte* le risorse create in questa esercitazione, inclusi il registro contenitori, l'insieme di credenziali delle chiavi e l'entità servizio, eseguire questi comandi. Tali risorse, tuttavia, vengono usate nell'[esercitazione successiva](container-registry-tutorial-build-task.md) della serie ed è quindi consigliabile mantenerle se si intende passare direttamente a tale esercitazione.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato il ciclo interno con una compilazione rapida, configurare un'**attività di compilazione** per attivare compilazioni delle immagini dei contenitori quando si esegue il commit di codice sorgente in un repository Git:

> [!div class="nextstepaction"]
> [Attivare compilazioni automatiche con attività di compilazione](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
