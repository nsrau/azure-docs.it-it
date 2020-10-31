---
title: Configurare CI/CD con GitHub Actions
description: Informazioni su come distribuire il codice nel servizio app di Azure da una pipeline CI/CD con GitHub Actions. Personalizzare le attività di compilazione ed eseguire distribuzioni complesse.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure
ms.openlocfilehash: 6c768df964d46364a8ca501c078dbecaf1aaa21f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095561"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Eseguire la distribuzione nel servizio app usando GitHub Actions

Inizia a usare le [azioni di GitHub](https://help.github.com/en/articles/about-github-actions) per automatizzare il flusso di lavoro e distribuirlo in [app Azure servizio](overview.md) da GitHub. 

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non si ha un accesso, iscriversi [gratuitamente](https://github.com/join).  
- Un'app di servizio app Azure funzionante. 
    - .NET: [creare un'app web ASP.NET Core in Azure](quickstart-dotnetcore.md)
    - ASP.NET: [creare un'app web ASP.NET Framework in Azure](quickstart-dotnet-framework.md)
    - JavaScript: [creare un'app web Node.js nel servizio app Azure](quickstart-nodejs.md)  
    - Java: [creare un'app Java nel servizio app Azure](quickstart-java.md)
    - Python: [creare un'app Python nel servizio app Azure](quickstart-python.md)

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. definire un'entità servizio o un profilo di pubblicazione. <br /> 2. Creare un segreto GitHub. |
|**Build** | 1. configurare l'ambiente. <br /> 2. compilare l'app Web. |
|**Distribuzione** | 1. distribuire l'app Web. |

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

## <a name="set-up-a-work-manually"></a>Configurare manualmente un lavoro

È anche possibile distribuire un flusso di lavoro senza usare il centro distribuzione. A tale scopo, sarà necessario prima generare le credenziali di distribuzione. 

## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

Il metodo consigliato per l'autenticazione con app Azure Services per le azioni di GitHub è con un profilo di pubblicazione. È anche possibile eseguire l'autenticazione con un'entità servizio, ma il processo richiede più passaggi. 

Salvare la credenziale del profilo di pubblicazione o l'entità servizio come [segreto GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) per l'autenticazione con Azure. Si accederà al segreto all'interno del flusso di lavoro. 

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/applevel)

Un profilo di pubblicazione è una credenziale a livello di app. Configurare il profilo di pubblicazione come segreto GitHub. 

1. Passare al servizio app nel portale di Azure. 

1. Nella pagina **Panoramica** selezionare **Ottieni profilo di pubblicazione** .

1. Salvare il file scaricato. Il contenuto del file verrà usato per creare un segreto GitHub.

> [!NOTE]
> A partire dal 2020 ottobre, per le app Web Linux è necessario impostare l'opzione app `WEBSITE_WEBDEPLOY_USE_SCM` su `true` **prima di scaricare il profilo di pubblicazione** . Questo requisito verrà rimosso in futuro.

# <a name="service-principal"></a>[Entità servizio](#tab/userlevel)

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova** .

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

---

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub


# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/applevel)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto** .

Per usare le [credenziali a livello di app](#generate-deployment-credentials), incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto. Denominare il segreto `AZURE_WEBAPP_PUBLISH_PROFILE` .

Quando si configura il flusso di lavoro di GitHub, si usa il `AZURE_WEBAPP_PUBLISH_PROFILE` nell'azione Distribuisci app Web di Azure. Ad esempio:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Entità servizio](#tab/userlevel)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto** .

Per usare le [credenziali a livello di utente](#generate-deployment-credentials), incollare l'intero output JSON dal comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

Quando in seguito si configura il file del flusso di lavoro, si usa il segreto come `creds` di input dell'azione di accesso di Azure. Ad esempio:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Configurare l'ambiente

La configurazione dell'ambiente può essere eseguita con una delle azioni di installazione.

|**Lingua**  |**Azione di installazione**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Negli esempi seguenti viene illustrato come configurare l'ambiente per le diverse lingue supportate:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Compilare l'app Web

Il processo di compilazione di un'app Web e la distribuzione di app Azure servizio cambiano a seconda del linguaggio. 

Negli esempi seguenti viene illustrata la parte del flusso di lavoro che compila l'app Web, in diverse lingue supportate.

Per tutte le lingue, è possibile impostare la directory radice dell'app Web con `working-directory` . 

**.NET**

La variabile `AZURE_WEBAPP_PACKAGE_PATH` di ambiente imposta il percorso del progetto dell'app Web. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

È possibile ripristinare le dipendenze NuGet ed eseguire MSBuild con `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Per Node.js, è possibile impostare `working-directory` o modificare per la directory NPM in `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Eseguire la distribuzione nel servizio app

Per distribuire il codice in un'app del servizio app, usare l'azione `azure/webapps-deploy@v2`. Questa azione ha quattro parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **app-name** | (Obbligatorio) Nome dell'app del servizio app | 
| **publish-profile** | (Facoltativo) Contenuto del file del profilo di pubblicazione con segreti Distribuzione Web |
| **package** | (Facoltativo) Percorso del pacchetto o della cartella. Il percorso può includere *. zip, *. War, *. jar o una cartella da distribuire |
| **slot-name** | Opzionale Immettere uno slot esistente diverso dallo [slot](deploy-staging-slots.md) di produzione |


# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/applevel)

### <a name="net-core"></a>.NET Core

Consente di compilare e distribuire un'app .NET Core in Azure usando un profilo di pubblicazione di Azure. L' `publish-profile` input fa riferimento al `AZURE_WEBAPP_PUBLISH_PROFILE` segreto creato in precedenza.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Compilare e distribuire un'app MVC ASP.NET che usa NuGet e `publish-profile` per l'autenticazione. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Consente di compilare e distribuire un'app Java Spring in Azure usando un profilo di pubblicazione di Azure. L' `publish-profile` input fa riferimento al `AZURE_WEBAPP_PUBLISH_PROFILE` segreto creato in precedenza.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Per distribuire un `war` anziché un `jar` , modificare il `package` valore. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Consente di compilare e distribuire un'app Node.js in Azure usando il profilo di pubblicazione dell'app. L' `publish-profile` input fa riferimento al `AZURE_WEBAPP_PUBLISH_PROFILE` segreto creato in precedenza.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Consente di compilare e distribuire un'app Python in Azure usando il profilo di pubblicazione dell'app. Si noti il modo in `publish-profile` cui l'input fa riferimento al `AZURE_WEBAPP_PUBLISH_PROFILE` segreto creato in precedenza.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Entità servizio](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Creare e distribuire un'app .NET Core in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Creare e distribuire un'app ASP.NET MVC in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Creare e distribuire un'app Java Spring in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Creare e distribuire un'app Node.js in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

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
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Creare e distribuire un'app Python in Azure usando un'entità servizio di Azure. Si noti il modo in `creds` cui l'input fa riferimento al `AZURE_CREDENTIALS` segreto creato in precedenza.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
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
