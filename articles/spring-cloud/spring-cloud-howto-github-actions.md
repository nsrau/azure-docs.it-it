---
title: Azure Spring Cloud CI/CD with GitHub Actions
description: Come creare il flusso di lavoro CI/CD per Azure Spring Cloud con le azioni GitHubHow to build up CI/CD workflow for Azure Spring Cloud with GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538465"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD with GitHub Actions

Le azioni GitHub supportano un flusso di lavoro automatizzato del ciclo di vita dello sviluppo software. Con GitHub Actions for Azure Spring Cloud è possibile creare flussi di lavoro nel repository per compilare, testare, creare pacchetti, rilasciare e distribuire in Azure.With GitHub Actions for Azure Spring Cloud you can create workflows in your repository to build, test, package, release, and deploy to Azure. 

## <a name="prerequisites"></a>Prerequisiti
Questo esempio richiede l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="set-up-github-repository-and-authenticate"></a>Configurare il repository GitHub e l'autenticazione
Per autorizzare l'azione di accesso di Azure sono necessarie credenziali per principio del servizio di Azure.You need an Azure service principle credential to authorize Azure login action. Per ottenere una credenziale di Azure, eseguire i comandi seguenti nel computer locale:To get an Azure credential, execute the following commands on your local machine:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Per accedere a un gruppo di risorse specifico, è possibile ridurre l'ambito:To access to a specific resource group, you can reduce the scope:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Il comando deve restituire un oggetto JSON:The command should output a JSON object:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Questo esempio usa l'esempio Piggy Metrics in GitHub.This example uses the [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) sample on GitHub.  Fare clic sull'esempio, aprire la pagina del **Secrets** repository GitHub **Add a new secret**e fare clic sulla scheda **Impostazioni.**

 ![Aggiungi nuovo segreto](./media/github-actions/actions1.png)

Impostare il `AZURE_CREDENTIALS` nome del segreto su e il relativo valore sulla stringa JSON trovata sotto l'intestazione *Configurare il repository GitHub ed eseguire l'autenticazione.*

 ![Impostare i dati segreti](./media/github-actions/actions2.png)

È anche possibile ottenere le credenziali di accesso di Azure dall'insieme di credenziali delle chiavi nelle azioni di GitHub, come illustrato in Autentica la primavera di Azure con l'insieme di credenziali delle chiavi in Azioni GitHub .You can also get the Azure login credential from Key Vault in GitHub actions as explained in [Authenticate Azure Spring with Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Eseguire il provisioning dell'istanza del servizioProvision service instance
Per eseguire il provisioning dell'istanza del servizio Cloud di Azure Spring, eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.To provision your Azure Spring Cloud service instance, run the following commands using the Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Creare il flusso di lavoroBuild the workflow
Il flusso di lavoro viene definito utilizzando le opzioni seguenti.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparare la distribuzione con l'interfaccia della riga di comando di AzurePrepare for deployment with Azure CLI
Il `az spring-cloud app create` comando non è attualmente idempotente.  È consigliabile questo flusso di lavoro nelle istanze e nelle app di Azure Spring Cloud esistenti.

Usare i comandi dell'interfaccia della riga di comando di Azure seguenti per la preparazione:Use the following Azure CLI commands for preparation:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuire direttamente con l'interfaccia della riga di comando di AzureDeploy with Azure CLI directly
Creare `.github/workflow/main.yml` il file nel repository:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Eseguire la distribuzione con l'azione dell'interfaccia della riga di comando di AzureDeploy
Il `run` comando az utilizzerà la versione più recente dell'interfaccia della riga di comando di Azure.The az command will use the latest version of Azure CLI. Se sono presenti modifiche di rilievo, è anche possibile usare `action`una versione specifica dell'interfaccia della riga di comando di Azure con azure/CLI. 

> [!Note] 
> Questo comando verrà eseguito in `env` un nuovo contenitore, quindi non funzionerà e l'accesso ai file tra azioni potrebbe avere restrizioni aggiuntive.

Creare il file .github/workflow/main.yml nel repository:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Distribuzione con Maven Plugin
Un'altra opzione consiste nell'utilizzare il [plug-in Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) per la distribuzione del Jar e l'aggiornamento delle impostazioni dell'app. Il `mvn azure-spring-cloud:deploy` comando è idempotente e creerà automaticamente le app, se necessario. Non è necessario creare app corrispondenti in anticipo.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Eseguire il flusso di lavoro
**GitHub Azioni** devono essere abilitate automaticamente dopo aver eseguito il push a GitHub.GitHub Actions should be enabled automatically after you push `.github/workflow/main.yml` to GitHub. L'azione verrà attivata quando si esegue il push di un nuovo commit. Se si crea questo file nel browser, l'azione dovrebbe essere già stata eseguita.

Per verificare che l'azione sia stata abilitata, fare clic sulla scheda **Azioni** nella pagina del repository GitHub:

 ![Verificare l'azione abilitata](./media/github-actions/actions3.png)

Se l'azione viene eseguita per errore, ad esempio se non è stata impostata la credenziale di Azure, è possibile eseguire nuovamente i controlli dopo aver risolto l'errore. Nella pagina del repository GitHub fare clic su **Azioni**, selezionare l'attività specifica del flusso di lavoro e quindi fare clic sul pulsante **Riesegui controlli** per rieseguire i controlli:

 ![Eseguire nuovamente i controlli](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Passaggi successivi
* [Key Vault per le azioni di Spring Cloud GitHubKey Vault for Spring Cloud GitHub actions](./spring-cloud-github-actions-key-vault.md)
* [Entità servizio di Azure Active DirectoryAzure Active Directory service principals](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub Actions for Azure](https://github.com/Azure/actions/)
