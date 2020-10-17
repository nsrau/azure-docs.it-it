---
title: Integrazione continua/distribuzione continua del contenitore personalizzato dalle azioni di GitHub
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore Linux personalizzato al servizio app da una pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: d6f66993b0fb7f97c551f4fbcb305111cfb2097e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150273"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuire un contenitore personalizzato nel servizio app usando le azioni di GitHub

Le [azioni di GitHub](https://help.github.com/en/articles/about-github-actions) offrono la flessibilità necessaria per creare un flusso di lavoro di sviluppo di software automatizzato. Con l' [azione distribuzione Web di Azure](https://github.com/Azure/webapps-deploy), è possibile automatizzare il flusso di lavoro per distribuire contenitori personalizzati nel [servizio app](overview.md) usando le azioni di GitHub.

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e parametri presenti nel flusso di lavoro.

Per un flusso di lavoro del contenitore del servizio app Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. recuperare un'entità servizio o un profilo di pubblicazione. <br /> 2. creare un segreto GitHub. |
|**Build** | 1. creare l'ambiente. <br /> 2. compilare l'immagine del contenitore. |
|**Distribuzione** | 1. distribuire l'immagine del contenitore. |

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Un account GitHub. Se non si ha un accesso, iscriversi [gratuitamente](https://github.com/join).  
- Un registro contenitori funzionante e un'app di servizio app Azure per i contenitori. Questo esempio USA Container Registry di Azure. 
    - [Informazioni su come creare un'applicazione Node.js in contenitori con Docker, eseguire il push dell'immagine del contenitore in un registro e quindi distribuire l'immagine nel servizio app Azure](/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Genera credenziali di distribuzione

Il metodo consigliato per l'autenticazione con app Azure Services per le azioni di GitHub è con un profilo di pubblicazione. È anche possibile eseguire l'autenticazione con un'entità servizio, ma il processo richiede più passaggi. 

Salvare la credenziale del profilo di pubblicazione o l'entità servizio come [segreto GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) per l'autenticazione con Azure. Si accederà al segreto all'interno del flusso di lavoro. 

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

Un profilo di pubblicazione è una credenziale a livello di app. Configurare il profilo di pubblicazione come segreto GitHub. 

1. Passare al servizio app nel portale di Azure. 

1. Nella pagina **Panoramica** selezionare **Ottieni profilo di pubblicazione**.

1. Salvare il file scaricato. Il contenuto del file verrà usato per creare un segreto GitHub.

# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

È possibile creare un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) nell'interfaccia della riga di comando di [Azure](/cli/azure/). Eseguire questo comando con [Azure cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Nell'esempio sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app. Copiare questo oggetto JSON per un momento successivo.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

---

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

Incollare il contenuto dell'output JSON come valore della variabile segreta. Assegnare al segreto il nome come `AZURE_CREDENTIALS` .

Quando si configura il file del flusso di lavoro in un secondo momento, si usa il segreto per l'input `creds` dell'azione di accesso di Azure. Ad esempio:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Configurare il segreto GitHub per l'autenticazione

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

Per usare le [credenziali a livello di app](#generate-deployment-credentials), incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto. Denominare il segreto `AZURE_WEBAPP_PUBLISH_PROFILE` .

Quando si configura il flusso di lavoro di GitHub, si usa il `AZURE_WEBAPP_PUBLISH_PROFILE` nell'azione Distribuisci app Web di Azure. Ad esempio:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

Per usare le [credenziali a livello di utente](#generate-deployment-credentials), incollare l'intero output JSON dal comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome come `AZURE_CREDENTIALS` .

Quando si configura il file del flusso di lavoro in un secondo momento, si usa il segreto per l'input `creds` dell'azione di accesso di Azure. Ad esempio:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Configurare i segreti GitHub per il registro

Definire i segreti da usare con l'azione Docker login. 

1. Passare al contenitore nell'portale di Azure o Docker e copiare il nome utente e la password. 

2. Definire un nuovo segreto per il nome utente del registro di sistema denominato `REGISTRY_USERNAME` . 

3. Definire un nuovo segreto per la password del registro di sistema denominata `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Nell'esempio seguente viene mostrata una parte del flusso di lavoro che compila un'immagine di Docker Node.JS. Usare [Docker login](https://github.com/azure/docker-login) per accedere a un registro contenitori privato. Questo esempio USA Azure Container Registry ma la stessa azione funziona anche per altri registri. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

È anche possibile usare [Docker login](https://github.com/azure/docker-login) per accedere contemporaneamente a più registri contenitori. Questo esempio include due nuovi segreti GitHub per l'autenticazione con docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Eseguire la distribuzione in un contenitore del servizio app

Per distribuire l'immagine in un contenitore personalizzato nel servizio app, usare l' `azure/webapps-deploy@v2` azione. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **app-name** | (Obbligatorio) Nome dell'app del servizio app | 
| **publish-profile** | (Facoltativo) Contenuto del file del profilo di pubblicazione con segreti Distribuzione Web |
| **images** | Nome/i completo delle immagini del contenitore. Ad esempio,' myregistry.azurecr.io/nginx:latest ' o ' Python: 3.7.2-Alpine/'. Per scenari a più contenitori è possibile specificare più nomi di immagini del contenitore (separati da più righe) |
| **slot-name** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |
| **file di configurazione** | Opzionale Percorso del file di Docker-Compose |

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Passaggi successivi

Il set di azioni raggruppate in repository diversi è disponibile in GitHub. Ogni repository contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Azioni flussi di lavoro da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [App Web di Azure](https://github.com/Azure/webapps-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventi che attivano i flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione Kubernetes](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro introduttivi](https://github.com/actions/starter-workflows)