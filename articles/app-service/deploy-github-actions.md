---
title: Configurare CI/CD con GitHub Actions
description: Informazioni su come distribuire il codice nel servizio app di Azure da una pipeline CI/CD con GitHub Actions. Personalizzare le attività di compilazione ed eseguire distribuzioni complesse.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 54e4ce409eb9f2a6bedd7861b3e268311f886b49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273246"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Eseguire la distribuzione nel servizio app usando GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con le azioni del servizio app di Azure per GitHub è possibile automatizzare il flusso di lavoro per la distribuzione nel [servizio app di Azure](overview.md) usando GitHub Actions.

> [!IMPORTANT]
> GitHub Actions è attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando l'account GitHub.
> 

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

## <a name="use-the-deployment-center"></a>Usare il centro distribuzione

È possibile iniziare rapidamente a usare le azioni di GitHub tramite il centro distribuzione del servizio app. Verrà generato automaticamente un file del flusso di lavoro in base allo stack dell'applicazione ed eseguito il commit nel repository GitHub nella directory corretta.

1. Passare a webapp nel portale di Azure
1. Sul lato sinistro fare clic su **centro distribuzione**
1. In **distribuzione continua (ci/CD)** selezionare **GitHub**
1. Selezionare quindi **azioni di GitHub**
1. Usare gli elenchi a discesa per selezionare il repository GitHub, il ramo e lo stack di applicazioni
    - Se il ramo selezionato è protetto, è comunque possibile continuare ad aggiungere il file del flusso di lavoro. Prima di continuare, assicurarsi di controllare le protezioni dei rami.
1. Nella schermata finale è possibile verificare le selezioni e visualizzare in anteprima il file del flusso di lavoro di cui verrà eseguito il commit nel repository. Se le selezioni sono corrette, fare clic su **fine** .

Verrà eseguito il commit del file del flusso di lavoro nel repository. Il flusso di lavoro per compilare e distribuire l'app viene avviato immediatamente.

## <a name="add-the-workflow-manually"></a>Aggiungere manualmente il flusso di lavoro

Per un flusso di lavoro del servizio app di Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. definire un'entità servizio. <br /> 2. creare un segreto GitHub. |
|**Build** | 1. configurare l'ambiente. <br /> 2. compilare l'app Web. |
|**Distribuzione** | 1. distribuire l'app Web. |

## <a name="generate-deployment-credentials"></a>Genera credenziali di distribuzione

# <a name="user-level-credentials"></a>[Credenziali a livello di utente](#tab/userlevel)

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'[interfaccia della riga di comando di Azure](/cli/azure/). È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che consentono di accedere all'app del servizio app in modo simile a quanto riportato di seguito. Copiare questo oggetto JSON per un momento successivo.

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

# <a name="app-level-credentials"></a>[Credenziali a livello di app](#tab/applevel)

È possibile usare le credenziali a livello di app usando il profilo di pubblicazione per l'app. Passare alla pagina di gestione dell'app nel portale. Nella pagina **Panoramica** fare clic sull'opzione **Ottieni profilo di pubblicazione** .

Il contenuto del file sarà necessario in un secondo momento.

---

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

# <a name="user-level-credentials"></a>[Credenziali a livello di utente](#tab/userlevel)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

Per usare le [credenziali a livello di utente](#generate-deployment-credentials), incollare l'intero output JSON dal comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome come `AZURE_CREDENTIALS` .

Quando si configura il file del flusso di lavoro in un secondo momento, si usa il segreto per l'input `creds` dell'azione di accesso di Azure. Ad esempio:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Credenziali a livello di app](#tab/applevel)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

Per usare le [credenziali a livello di app](#generate-deployment-credentials), incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto. Assegnare al segreto il nome come `azureWebAppPublishProfile` .

Quando si configura il file del flusso di lavoro in un secondo momento, si usa il segreto per l'input `publish-profile` dell'azione Distribuisci app Web di Azure. Ad esempio:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Configurare l'ambiente

La configurazione dell'ambiente può essere eseguita con una delle azioni di installazione.

|**Lingua**  |**Azione di installazione**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Gli esempi seguenti illustrano la parte del flusso di lavoro che configura l'ambiente per i vari linguaggi supportati:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Compilare l'app Web

Questo dipende dal linguaggio. Per i linguaggi supportati dal servizio app di Azure, questa sezione rappresenta la procedura di compilazione standard per ogni linguaggio.

Gli esempi seguenti illustrano la parte del flusso di lavoro che compila l'app Web, nei vari linguaggi supportati.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Eseguire la distribuzione nel servizio app

Per distribuire il codice in un'app del servizio app, usare l'azione `azure/webapps-deploy@v2`. Questa azione ha quattro parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **app-name** | (Obbligatorio) Nome dell'app del servizio app | 
| **publish-profile** | (Facoltativo) Contenuto del file del profilo di pubblicazione con segreti Distribuzione Web |
| **package** | (Facoltativo) Percorso del pacchetto o della cartella. *.zip, *.war, *.jar o una cartella per la distribuzione |
| **slot-name** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |

# <a name="user-level-credentials"></a>[Credenziali a livello di utente](#tab/userlevel)

Di seguito è riportato un flusso di lavoro di esempio per la distribuzione e la compilazione di un'app Node.js in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[Credenziali a livello di app](#tab/applevel)

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Node.js in Azure usando il profilo di pubblicazione dell'app. Si noti il modo in `publish-profile` cui l'input fa riferimento al `azureWebAppPublishProfile` segreto creato in precedenza.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="next-steps"></a>Passaggi successivi

Il set di azioni raggruppate in repository diversi è disponibile in GitHub. Ogni repository contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Azioni flussi di lavoro da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [App Web di Azure](https://github.com/Azure/webapps-deploy)

- [App Web per contenitori di Azure](https://github.com/Azure/webapps-container-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventi che attivano i flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione Kubernetes](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro introduttivi](https://github.com/actions/starter-workflows)