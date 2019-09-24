---
title: 'Esercitazione: Compilare immagini dei contenitori nel cloud - Attività del Registro Azure Container'
description: Questa esercitazione illustra come compilare un'immagine del contenitore Docker in Azure con Attività del Registro Azure Container e quindi distribuire tale immagine in Istanze di Azure Container.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 091c1a4c9e6adae69ec1c8b3e507624b9f5e6a96
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057503"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Esercitazione: Compilare e distribuire immagini dei contenitori nel cloud con Attività del Registro Azure Container

**ACR Tasks** è un gruppo di funzionalità in Registro Azure Container che consente una compilazione efficiente e semplificata delle immagini dei contenitori Docker in Azure. Questo articolo illustra come usare la funzionalità di *attività rapida* di ACR Tasks.

Il ciclo di sviluppo "interno" è il processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente. Un'attività rapida estende il ciclo interno al cloud, con convalida del completamento della compilazione e push automatico delle immagini compilate correttamente nel registro contenitori. Le immagini vengono compilate in modo nativo nel cloud, in prossimità del registro, in modo da velocizzare la distribuzione.

Tutte le competenze acquisite nei documenti Dockerfile sono trasferibili direttamente ad ACR Tasks. Per la compilazione nel cloud con ACR Tasks non è necessario modificare i documenti Dockerfile, ma solo il comando che viene eseguito. 

In questa esercitazione, che è la prima parte di una serie, si apprenderà come:

> [!div class="checklist"]
> * Ottenere il codice sorgente dell'applicazione di esempio
> * Compilare un'immagine del contenitore in Azure
> * Distribuire un contenitore in Istanze di Azure Container

Le esercitazioni successive illustrano come usare ACR Tasks per automatizzare la compilazione delle immagini dei contenitori in caso di commit del codice e di aggiornamento delle immagini di base. Le funzionalità di Attività del Registro Azure Container consentono anche di eseguire [attività in più passi](container-registry-tasks-multi-step.md), usando un file YAML per definire i passaggi per creare, eseguire il push e facoltativamente testare più contenitori.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario che sia installata la versione **2.0.46** o successiva e che sia stato eseguito l'accesso con [az login][az-login]. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="prerequisites"></a>Prerequisiti

### <a name="github-account"></a>Account GitHub

Se non se ne ha già uno, creare un account in https://github.com. In questa serie di esercitazioni viene usato un repository GitHub per illustrare la compilazione automatica di immagini in ACR Tasks.

### <a name="fork-sample-repository"></a>Creare una copia tramite fork del repository di esempio

Usare quindi l'interfaccia utente di GitHub per creare una copia tramite fork del repository di esempio nell'account GitHub. In questa esercitazione si compila un'immagine del contenitore dall'origine nel repository, mentre nell'esercitazione successiva si esegue il push di un commit nel fork del repository per avviare un'attività automatica.

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

## <a name="build-in-azure-with-acr-tasks"></a>Compilare in Azure con ACR Tasks

Dopo aver eseguito il pull del codice sorgente nel computer in uso, seguire questa procedura per creare un registro contenitori e compilare l'immagine del contenitore con Attività del Registro Azure Container.

Per semplificare l'esecuzione dei comandi di esempio, nelle esercitazioni di questa serie vengono usate variabili di ambiente della shell. Eseguire il comando seguente per impostare la variabile `ACR_NAME`. Sostituire **\<registry-name\>** con un nome univoco per il nuovo registro contenitori. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici in minuscolo. Dato che le altre risorse create in questa esercitazione sono basate su questo nome, sarà necessario modificare solo questa prima variabile.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Dopo aver popolato la variabile di ambiente del registro contenitori, sarà possibile copiare e incollare il resto dei comandi dell'esercitazione senza modificare alcun valore. Eseguire questi comandi per creare un gruppo di risorse e un registro contenitori:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Ora che è disponibile un registro, usare Attività del Registro Azure Container per compilare un'immagine del contenitore dal codice di esempio. Eseguire il comando [az acr build][az-acr-build] per eseguire un'*attività rapida*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

L'output del comando [az acr build][az-acr-build] è simile al seguente. Vengono visualizzati il caricamento del codice sorgente ("context") in Azure e i dettagli dell'operazione `docker build` eseguita da ACR Tasks nel cloud. Dato che ACR Tasks usa `docker build` per compilare le immagini, è possibile iniziare a usare subito ACR Tasks senza apportare alcuna modifica ai documenti Dockerfile.

```console
$ az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2018/08/22 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2018/08/22 18:31:42 Setting up Docker configuration...
2018/08/22 18:31:43 Successfully set up Docker configuration
2018/08/22 18:31:43 Logging in to registry: myregistry.azurecr.io
2018/08/22 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2018/08/22 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Nella parte finale dell'output, ACR Tasks mostra le dipendenze individuate per l'immagine. ACR Tasks può così automatizzare la compilazione di immagini in caso di aggiornamenti dell'immagine di base, ad esempio quando un'immagine di base viene aggiornata con patch del sistema operativo o del framework. Il supporto di ACR Tasks per gli aggiornamenti delle immagini di base verrà illustrato più avanti in questa serie di esercitazioni.

## <a name="deploy-to-azure-container-instances"></a>Eseguire la distribuzione in Istanze di Azure Container

Per impostazione predefinita, ACR Tasks esegue automaticamente il push delle immagini compilate correttamente nel registro, dal quale possono essere distribuite immediatamente.

In questa sezione si creano un'istanza di Azure Key Vault e un'entità servizio e quindi si distribuisce il contenitore in Istanze di Azure Container usando le credenziali dell'entità servizio.

### <a name="configure-registry-authentication"></a>Configurare l'autenticazione del registro

In tutti gli scenari di produzione dovrebbero essere usate [entità servizio][service-principal-auth] per accedere a un registro contenitori di Azure. Mediante le entità servizio è possibile fornire il controllo degli accessi in base al ruolo alle immagini del contenitore. Ad esempio, è possibile configurare un'entità servizio con accesso pull-only a un registro.

#### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Se non si ha già un insieme di credenziali delle chiavi in [Azure Key Vault](/azure/key-vault/), crearne uno usando i comandi seguenti nell'interfaccia della riga di comando di Azure.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Creare un'entità servizio e archiviare le credenziali

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
                --role acrpull \
                --query password \
                --output tsv)
```

L'argomento `--role` nel comando precedente configura l'entità servizio con il ruolo *acrpull*, che concede l'accesso al registro con autorizzazioni solo di pull. Per concedere l'accesso con autorizzazioni sia di push che di pull, impostare l'argomento `--role` su *acrpush*.

Archiviare quindi nell'insieme di credenziali il valore *appId* dell'entità servizio, che è il **nome utente** passato a Registro Azure Container per l'autenticazione:

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

### <a name="deploy-a-container-with-azure-cli"></a>Distribuire un contenitore con l'interfaccia della riga di comando di Azure

Ora che le credenziali dell'entità servizio sono archiviate come segreti di Azure Key Vault, le applicazioni e i servizi possono usarle per accedere al registro privato.

Eseguire il comando [az container create][az-container-create] seguente per distribuire un'istanza di contenitore. Il comando usa le credenziali dell'entità servizio archiviate in Azure Key Vault per eseguire l'autenticazione al registro contenitori.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Dato che il valore di `--dns-name-label` deve essere univoco in Azure, il comando precedente aggiunge il nome del registro contenitori all'etichetta del nome DNS del contenitore. L'output del comando visualizza il nome di dominio completo (FQDN) del contenitore, ad esempio:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-tasks \
>     --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-tasks-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Prendere nota del nome di dominio completo del contenitore, perché verrà usato nella sezione successiva.

### <a name="verify-the-deployment"></a>Verificare la distribuzione

Per controllare il processo di avvio del contenitore, usare il comando [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-tasks
```

L'output di `az container attach` mostra per prima cosa lo stato del contenitore durante il pull dell'immagine e l'avvio, quindi associa STDOUT e STDERR della console locale a STDOUT e STDERR del contenitore.

```console
$ az container attach --resource-group $RES_GROUP --name acr-tasks
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2018-08-22 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Quando viene visualizzato `Server running at http://localhost:80`, passare al nome di dominio completo del contenitore nel browser per visualizzare l'applicazione in esecuzione. Il nome di dominio completo dovrebbe essere visualizzato nell'output del comando `az container create` eseguito nella sezione precedente.

![Screenshot del rendering dell'applicazione di esempio nel browser][quick-build-02-browser]

Per scollegare la console dal contenitore, premere `Control+C`.

## <a name="clean-up-resources"></a>Pulire le risorse

Arrestare l'istanza del contenitore con il comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Per rimuovere *tutte* le risorse create in questa esercitazione, inclusi il registro contenitori, l'insieme di credenziali delle chiavi e l'entità servizio, eseguire questi comandi. Tali risorse, tuttavia, vengono usate nell'[esercitazione successiva](container-registry-tutorial-build-task.md) della serie ed è quindi consigliabile mantenerle se si intende passare direttamente a tale esercitazione.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato il ciclo interno con un'attività rapida, configurare un'**attività di compilazione** per attivare compilazioni delle immagini dei contenitori quando si esegue il commit di codice sorgente in un repository Git:

> [!div class="nextstepaction"]
> [Attivare compilazioni automatiche con attività](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
