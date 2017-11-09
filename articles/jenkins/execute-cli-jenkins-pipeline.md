---
title: Eseguire l'interfaccia della riga di comando di Azure con Jenkins | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per distribuire un'app web di Java in Azure in Jenkins Pipeline
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: b93e787050613b241ea116e7263f63835bd211fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Distribuire nel servizio app di Azure con Jenkins e l'interfaccia della riga di comando di Azure
Per distribuire un'app Web di Java in Azure, è possibile usare l'interfaccia della riga di comando di Azure in [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). In questa esercitazione viene creata una pipeline CI/CD in una macchina virtuale di Azure e viene illustrato come:

> [!div class="checklist"]
> * Creare una macchina virtuale Jenkins
> * Configurare Jenkins
> * Creare un'app Web in Azure
> * Preparare un repository GitHub
> * Creare una pipeline Jenkins
> * Eseguire la pipeline e verificare l'app Web

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Creare e configurare un'istanza di Jenkins
Se si dispone già di un master di Jenkins, iniziare con il [modello di soluzione](install-jenkins-solution-template.md) che include il plug-in delle [credenziali di Azure](https://plugins.jenkins.io/azure-credentials) richiesto per impostazione predefinita. 

Il plug-in delle credenziali di Azure consente di archiviare le credenziali dell'entità servizio di Microsoft Azure in Jenkins. Nella versione 1.2 è stato aggiunto il supporto affinché Jenkins Pipeline possa ottenere le credenziali di Azure. 

Assicurarsi di disporre della versione 1.2 o versioni successive:
* Nel dashboard di Jenkins, fare clic su **Manage Jenkins -> Plugin Manager ->** e cercare **Azure Credential**. 
* Se la versione è precedente alla 1.2, aggiornare il plug-in.

Anche Java JDK e Maven sono necessari nel master di Jenkins. Per l'installazione, accedere al master di Jenkins usando SSH ed eseguire i comandi seguenti:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Aggiungere l'entità servizio di Azure alla credenziale di Jenkins

Per eseguire l'interfaccia della riga di comando di Azure è necessaria una credenziale di Azure.

* Nel dashboard di Jenkins fare clic su **Credentials -> System ->**. Fare clic su **Global credentials(unrestricted)**.
* Fare clic su **Add Credentials** per aggiungere un'[entità servizio di Microsoft Azure](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json), immettendo: Subscription ID (ID sottoscrizione), Client ID (ID client), Client Secret (Segreto client) e OAuth 2.0 Token Endpoint (Endpoint di token OAuth 2.0). Indicare un ID per l'uso nel passaggio successivo.

![Add Credentials](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Creare un servizio app di Azure per la distribuzione dell'app Web di Java

Creare un piano di servizio app di Azure con il piano tariffario **GRATUITO** usando il comando dell'interfaccia della riga di comando [az appservice plan create](/cli/azure/appservice/plan#create). Il piano di servizio app definisce le risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono queste risorse, per poter consentire un risparmio sui costi quando si ospitano più app. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Quando il piano è pronto, l'output dell'interfaccia della riga di comando di Azure è simile all'esempio seguente:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Creare un'app Web di Azure

 Usare il comando dell'interfaccia della riga di comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) per creare la definizione di un'app Web nel piano di servizio app `myAppServicePlan`. La definizione dell'app Web fornisce un URL con cui accedere all'applicazione e configura diverse opzioni per distribuire il codice in Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Sostituire il segnaposto `<app_name>` con il nome univoco dell'app. Questo nome univoco fa parte del nome di dominio predefinito per l'app Web, quindi è necessario che sia univoco rispetto a tutte le app presenti in Azure. È possibile eseguire il mapping di una voce di nome di dominio personalizzata all'app Web prima di esporla agli utenti.

Quando la definizione dell'app Web è pronta, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurare Java 

Impostare la configurazione del runtime Java necessaria per l'app con il comando [az appservice web config update](/cli/azure/appservice/web/config#update).

Il comando seguente configura l'app Web per l'esecuzione in un'istanza recente di Java 8 JDK e in [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparare un repository GitHub
Aprire il repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Per creare il fork del repository nel proprio account GitHub, fare clic sul pulsante **Fork** nell'angolo superiore destro.

* Nell'interfaccia utente Web di GitHub, aprire il file **Jenkinsfile**. Fare clic sull'icona della matita per modificare il file per aggiornare il gruppo di risorse e il nome dell'app Web nella riga 20 e 21 rispettivamente.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Modificare la riga 23 per aggiornare l'ID delle credenziali nell'istanza di Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Creare una pipeline Jenkins
Aprire Jenkins in un Web browser, fare clic su **New Item**. 

* Specificare un nome per il processo e selezionare **Pipeline**. Fare clic su **OK**.
* Quindi fare clic sulla scheda **Pipeline**. 
* Per **Definition** selezionare **Pipeline script from SCM**.
* Per **SCM** selezionare **Git**.
* Immettere l'URL di GitHub per il repository con fork: https:\<your forked repo\>.git
* Fare clic su **Save**

## <a name="test-your-pipeline"></a>Testare la pipeline
* Passare alla pipeline creata e fare clic su **Build now**
* Una build dovrebbe avere esito positivo in pochi secondi ed è possibile passare alla build e fare clic su **Console Output** per visualizzare i dettagli

## <a name="verify-your-web-app"></a>Verificare l'app Web
Per verificare che il file WAR sia stato distribuito correttamente nell'app Web. Aprire un Web browser:

* Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Verranno visualizzati:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sostituire &lt;x> e &lt;y> con numeri qualsiasi) per ottenere la somma di x e y

![Calcolatrice: aggiungi](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Distribuire un'app Web di Azure in Linux
Ora che si è appreso come usare l'interfaccia della riga di comando di Azure nella pipeline di Jenkins, è possibile modificare lo script per la distribuzione di un'app Web di Azure in Linux.

L'app Web in Linux supporta un modo diverso di esecuzione della distribuzione, che consiste nell'usare Docker. Per la distribuzione è necessario fornire un Dockerfile che include l'app Web in runtime di servizio in un'immagine Docker. Il plug-in compilerà l'immagine, la inserirà in un registro Docker e la distribuirà nell'app Web.

* Seguire i passaggi indicati [qui](../app-service/containers/quickstart-nodejs.md) per creare un'app Web di Azure in esecuzione in Linux.
* Installare Docker nell'istanza Jenkins seguendo le istruzioni riportate in questo [articolo](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Creare un registro contenitori nel portale di Azure seguendo i passaggi indicati [qui](/azure/container-registry/container-registry-get-started-azure-cli).
* Nello stesso repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) con fork modificare il file **Jenkinsfile2**:
    * Riga 18-21, aggiornare rispettivamente i nomi del gruppo di risorse, l'app Web e il record di controllo di accesso. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Riga 24, aggiornare \<azsrvprincipal\> all'ID delle credenziali
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Creare una nuova pipeline Jenkins come per la distribuzione dell'app Web di Azure in Windows; solo questa volta usare invece **Jenkinsfile2**.
* Eseguire il nuovo processo.
* Per verificare, nell'interfaccia della riga di comando di Azure eseguire:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Si ottiene il risultato seguente:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Viene visualizzato il messaggio: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sostituire &lt;x> e &lt;y> con numeri qualsiasi) per ottenere la somma di x e y
    
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata configurata la pipeline Jenkins che estrae il codice sorgente nel repository GitHub. Esegue Maven per compilare un file WAR e quindi usa l'interfaccia della riga di comando di Azure per distribuire nel servizio App di Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Jenkins
> * Configurare Jenkins
> * Creare un'app Web in Azure
> * Preparare un repository GitHub
> * Creare una pipeline Jenkins
> * Eseguire la pipeline e verificare l'app Web
