---
title: Configurare CI/CD con GitHub Actions
description: Informazioni su come distribuire il codice nel servizio app di Azure da una pipeline CI/CD con GitHub Actions. Personalizzare le attività di compilazione ed eseguire distribuzioni complesse.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: tracking-python
ms.openlocfilehash: b40da0c8746bc63a99394027b61d777a611727e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559600"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Eseguire la distribuzione nel servizio app usando GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con le azioni del servizio app di Azure per GitHub è possibile automatizzare il flusso di lavoro per la distribuzione nel [servizio app di Azure](overview.md) usando GitHub Actions.

> [!IMPORTANT]
> GitHub Actions è attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando l'account GitHub.
> 

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del servizio app di Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Creare un'entità servizio <br /> 2. Creare un segreto GitHub |
|**Build** | 1. Configurare l'ambiente <br /> 2. Compilare l'app Web |
|**Distribuire** | 1. Distribuire l'app Web |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

In questo esempio, sostituire i segnaposto nella risorsa con il proprio ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è costituito dalle credenziali di assegnazione di ruolo che consentono l'accesso all'app del servizio app. Copiare questo oggetto JSON, che potrà essere usato per eseguire l'autenticazione da GitHub.

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. Questo è il motivo per cui nell'esempio precedente l'ambito è limitato all'app del servizio app specifica, e non include l'intero gruppo di risorse.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

È anche possibile usare le credenziali a livello di app, ad esempio il profilo di pubblicazione per la distribuzione. Per configurare il segreto seguire questa procedura:

1. Scaricare il profilo di pubblicazione per l'app del servizio app dal portale usando l'opzione **Recupera profilo di pubblicazione**.

2. In [GitHub](https://github.com/) esplorare il repository e selezionare **Settings > Secrets > Add a new secret** (Impostazioni > Segreti > Aggiungi un nuovo segreto).

    ![chiavi private](media/app-service-github-actions/secrets.png)

3. Incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto.

4. A questo punto, nel file del flusso di lavoro nel ramo `.github/workflows/workflow.yml` sostituire il segreto all'input `publish-profile` dell'azione Distribuisci app Web di Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Dopo essere stato definito, il segreto viene visualizzato come illustrato di seguito.

    ![chiavi private](media/app-service-github-actions/app-service-secrets.png)

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

### <a name="deploy-using-publish-profile"></a>Distribuire usando il profilo di pubblicazione

Di seguito è riportato un flusso di lavoro di esempio per la distribuzione e la compilazione di un'app Node.js in Azure usando il profilo di pubblicazione.

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

### <a name="deploy-using-azure-service-principal"></a>Distribuire usando l'entità servizio di Azure

Di seguito è riportato un flusso di lavoro di esempio per la distribuzione e la compilazione di un'app Node.js in Azure usando un'entità servizio di Azure.

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

## <a name="next-steps"></a>Passaggi successivi

Il set di azioni raggruppate in repository diversi è disponibile in GitHub. Ogni repository contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Flussi di lavoro di azioni per la distribuzione in Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [App Web di Azure](https://github.com/Azure/webapps-deploy)

- [App Web per contenitori di Azure](https://github.com/Azure/webapps-container-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventi che attivano i flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione Kubernetes](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro introduttivi](https://github.com/actions/starter-workflows)
