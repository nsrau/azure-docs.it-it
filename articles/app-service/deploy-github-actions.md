---
title: Configurare CI/CD con le azioni GitHubConfigure CI/CD with GitHub Actions
description: Informazioni su come distribuire il codice nel servizio app di Azure da una pipeline CI/CD con azioni GitHub.Learn how to deploy your code to Azure App Service from a CI/CD pipeline with GitHub Actions. Personalizzare le attività di compilazione ed eseguire distribuzioni complesse.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084992"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Eseguire la distribuzione nel servizio app usando le azioni GitHubDeploy to App Service using GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita dello sviluppo software. Con le azioni del servizio app di Azure per GitHub, è possibile automatizzare il flusso di lavoro per distribuire nel servizio app di Azure usando le azioni GitHub.With the Azure App Service Actions for GitHub, you can automate your workflow to deploy to [Azure App Service](overview.md) using GitHub Actions.

> [!IMPORTANT]
> GitHub Actions è attualmente in versione beta. Devi prima [registrarti per unirti all'anteprima](https://github.com/features/actions) usando il tuo account GitHub.
> 

Un flusso di lavoro è definito da un file `/.github/workflows/` YAML (.yml) nel percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del servizio app di Azure, il file include tre sezioni:For an Azure App Service workflow, the file has three sections:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio <br /> 2. Creare un segreto GitHub |
|**Build** | 1. Configurare l'ambiente <br /> 2. Creare l'app Web |
|**Distribuire** | 1. Distribuire l'app Web |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'entità servizio usando il comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/) [service principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

In questo esempio sostituire i segnaposto nella risorsa con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app. Copiare questo oggetto JSON, che è possibile usare per l'autenticazione da GitHub.Copy this JSON object, which you can use to authenticate from GitHub.

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

> [!IMPORTANT]
> È sempre buona norma concedere un accesso minimo. Questo è il motivo per cui l'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

## <a name="configure-the-github-secret"></a>Configurare il segreto GitHubConfigure the GitHub secret

È anche possibile usare le credenziali a livello di app, ad esempio pubblicare il profilo per la distribuzione. Seguire i passaggi per configurare il segreto:

1. Scaricare il profilo di pubblicazione per l'app del servizio app dal portale usando l'opzione **Ottieni profilo di pubblicazione.**

2. In [GitHub](https://github.com/), sfogliare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto**

    ![chiavi private](media/app-service-github-actions/secrets.png)

3. Incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto.

4. Ora nel file del flusso `.github/workflows/workflow.yml` di lavoro nel `publish-profile` ramo: sostituire il segreto per l'input dell'azione Distribuisci App Web di Azure.Now in the workflow file in your branch: replace the secret for the input of the deploy Azure Web App action.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Viene visualizzato il segreto come mostrato di seguito una volta definito.

    ![chiavi private](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Configurare l'ambiente

La configurazione dell'ambiente può essere eseguita utilizzando una delle azioni di installazione.

|**Lingua**  |**Azione di installazione**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Negli esempi seguenti viene illustrato la parte del flusso di lavoro che configura l'ambiente per le varie lingue supportate:

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

## <a name="build-the-web-app"></a>Creare l'app Web

Questo dipende dalla lingua e per le lingue supportate dal servizio app di Azure, questa sezione deve essere la procedura di compilazione standard di ogni lingua.

Negli esempi seguenti viene illustrato la parte del flusso di lavoro che compila l'app Web nelle varie lingue supportate.

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
## <a name="deploy-to-app-service"></a>Distribuire nel servizio app

Per distribuire il codice a un'app del servizio app, usare l'azione. `azure/webapps-deploy@v2` Questa azione ha quattro parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **nome app** | (Obbligatorio) Nome dell'app del servizio app | 
| **pubblicare-profilo** | (Facoltativo) Pubblicare il contenuto del file del profilo con i segreti di distribuzione Web |
| **package** | (Facoltativo) Percorso del pacchetto o della cartella. .zip, .war, .jar o una cartella da distribuire |
| **nome-slot** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |

### <a name="deploy-using-publish-profile"></a>Eseguire la distribuzione usando il profilo di pubblicazioneDeploy using Publish Profile

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Node.js in Azure usando il profilo di pubblicazione.

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

### <a name="deploy-using-azure-service-principal"></a>Distribuire usando l'entità servizio di AzureDeploy using Azure service principal

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Node.js in Azure usando un'entità servizio di Azure.Below is the sample workflow to build and deploy a Node.js app to Azure using an Azure service principal.

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

È possibile trovare il set di azioni raggruppate in repository diversi in GitHub, ognuno contenente documentazione ed esempi per usare GitHub per CI/CD e distribuire le app in Azure.You can find our set of Actions grouped into different repositories on GitHub, each each containing documentation and examples to help you use GitHub for CI/CD and deploy your apps to Azure.

- [Flusso di lavoro azioni da distribuire in AzureActions workflow to deploy to Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [WebApp di Azure per i contenitoriAzure WebApp for containers](https://github.com/Azure/webapps-container-deploy)

- [Accesso/logout Docker](https://github.com/Azure/docker-login)

- [Eventi che attivano flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8 simplementazione](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro di avvio](https://github.com/actions/starter-workflows)
