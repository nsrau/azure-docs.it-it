---
title: Eseguire la distribuzione nel Servizio app di Azure con il plug-in Jenkins| Microsoft Docs
description: Informazioni su come usare il Servizio app di Azure per distribuire un'app Web Java in Azure con Jenkins
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b2035d6bc0d323f2497a1db9b88d3ed015235b16
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Eseguire la distribuzione nel Servizio app di Azure con il plug-in Jenkins 
Per distribuire un'app Web Java in Azure, è possibile usare l'interfaccia della riga di comando di Azure in [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) oppure il [plug-in Jenkins Servizio app di Azure](https://plugins.jenkins.io/azure-app-service). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare Jenkins per eseguire la distribuzione nel Servizio app di Azure tramite FTP 
> * Configurare Jenkins per eseguire la distribuzione nel Servizio app di Azure su Linux tramite Docker 

## <a name="create-and-configure-jenkins-instance"></a>Creare e configurare un'istanza di Jenkins
Se non è già disponibile un master Jenkins, iniziare con il [modello di soluzione](install-jenkins-solution-template.md) che include JDK8 e i seguenti plug-in necessari:

* [Plug-in Git client Jenkins](https://plugins.jenkins.io/git-client) 2.4.6 
* [Plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) 1.2
* [Servizio app di Azure](https://plugins.jenkins.io/azure-app-server) versione 0.1

È possibile usare il plug-in del servizio app per distribuire l'app Web in tutti i linguaggi, ad esempio C#, PHP, Java e node.js, supportati dal servizio app di Azure. In questa esercitazione viene usata l'app Java di esempio, [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Per creare il fork del repository nel proprio account GitHub, fare clic sul pulsante **Fork** nell'angolo superiore destro.  

Per compilare il progetto Java sono necessari Java JDK e Maven. Assicurarsi di installare i componenti nel master Jenkins o l'agente VM, se in uso per l'integrazione continua. 

Per l'installazione, accedere all'istanza di Jenkins usando SSH ed eseguire i comandi seguenti:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Per la distribuzione del servizio app in Linux, è necessario anche installare Docker sul master Jenkins o sull'agente VM usato per la compilazione. Leggere questo articolo per installare Docker: https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Aggiungere l'entità servizio di Azure alla credenziale di Jenkins

Per la distribuzione in Azure è necessaria un'entità servizio di Azure. 

<ol>
<li>Usare l'[interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) o il [portale di Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal) per creare un'entità servizio di Azure</li>
<li>Nel dashboard di Jenkins fare clic su **Credentials -> System ->**. Fare clic su **Global credentials(unrestricted)**.</li>
<li>Fare clic su **Add Credentials** (Aggiungi credenziali) per aggiungere un'entità servizio di Microsoft Azure immettendo: Subscription ID (ID sottoscrizione), Client ID (ID client), Client Secret (Segreto client) e OAuth 2.0 Token Endpoint (Endpoint di token OAuth 2.0). Indicare un ID, **mySp**, per l'uso nei passaggi successivi.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Plug-in Servizio app di Azure

Il plug-in Servizio app di Azure v1.0 supporta la distribuzione continua in App Web di Azure tramite:

* GIT e FTP
* Docker per App Web in Linux

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Configurare Jenkins per distribuire l'app Web tramite FTP usando il dashboard di Jenkins

Per distribuire il progetto in App Web di Azure, è possibile caricare gli artefatti di compilazione, ad esempio i file .war in Java, usando Git o FTP.

Prima di configurare il processo in Jenkins, è necessario un piano di servizio app di Azure e un'app Web per l'esecuzione dell'app Java.


1. Creare un piano di servizio app di Azure con il piano tariffario **GRATUITO** usando il comando dell'interfaccia della riga di comando [az appservice plan create](/cli/azure/appservice/plan#create). Il piano di servizio app definisce le risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono queste risorse, per poter consentire un risparmio sui costi quando si ospitano più app.
2. Creare un'app Web. È possibile usare il [portale di Azure](/azure/app-service-web/web-sites-configure) o il seguente comando Az dell'interfaccia della riga di comando:
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. Assicurarsi di impostare la configurazione del runtime Java di cui l'app necessita. Il comando dell'interfaccia della riga di comando di Azure seguente configura l'app Web per l'esecuzione in un'istanza recente di Java 8 JDK e in [Apache Tomcat](http://tomcat.apache.org/) 8.0.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Impostare il processo Jenkins


1. Creare un nuovo progetto **freestyle** nel dashboard di Jenkins
2. Configurare la **gestione del codice sorgente** da usare nel fork locale di [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) fornendo l'**URL del repository**. Ad esempio: http://github.com/&lt;yourID>/javawebappsample.
3. Aggiungere un'istruzione di compilazione per compilare il progetto usando Maven. A tale scopo aggiungere un **Execute shell**. Per questo esempio, è necessario un passaggio aggiuntivo per rinominare il file *.war nella cartella di destinazione in ROOT.war.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. Aggiungere un'azione post-compilazione selezionando **Publish an Azure Web App** (Pubblica un'App Web di Azure).
5. Fornire "mySp", l'entità servizio di Azure archiviata nel passaggio precedente.
6. Nella sezione **Configurazione app** scegliere l'app Web e il gruppo di risorse nella sottoscrizione. Il plug-in rileva automaticamente se l'app Web è basata su Windows o Linux. Per un'app Web basata su Windows, viene visualizzata l'opzione "Publish Files" (Pubblica file).
7. Compilare i file da distribuire, ad esempio un pacchetto war se si usa Java. La directory di origine e quella di destinazione sono facoltative. I parametri consentono di specificare le cartelle di origine e di destinazione durante il caricamento di file. L'app Web Java in Azure viene eseguita in un server Tomcat. Si carica il pacchetto war nella cartella webapps. Per questo esempio, impostare **Source Directory** (Directory di origine) su "target" e specificare "webapps" per **Target Directory** (Directory di destinazione).
8. Se si vuole eseguire la distribuzione in uno slot di diverso dalla produzione, è possibile anche impostare il nome **Slot**.
9. Salvare e compilare il progetto. L'app web viene distribuita in Azure al termine della compilazione.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Distribuire l'app Web tramite FTP usando la pipeline Jenkins

Il plug-in è pronto per la pipeline. È possibile fare riferimento a un esempio nel repository GitHub.

1. Nell'interfaccia utente Web di GitHub aprire il file **Jenkinsfile_ftp_plugin**. Fare clic sull'icona della matita per modificare il file aggiornando il gruppo di risorse e il nome dell'app Web rispettivamente nella riga 11 e 12.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Modificare la riga 14 per aggiornare l'ID delle credenziali nell'istanza di Jenkins.    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Creare una pipeline Jenkins

1. Aprire Jenkins in un Web browser, fare clic su **New Item**.
2. Specificare un nome per il processo e selezionare **Pipeline**. Fare clic su **OK**.
3. Quindi fare clic sulla scheda **Pipeline**.
4. Per **Definition** selezionare **Pipeline script from SCM**.
5. Per **SCM** selezionare **Git**. Immettere l'URL di GitHub per il repository con fork: https:&lt;your forked repo>.git
6. Aggiornare **Script Path** in "Jenkinsfile_ftp_plugin"
7. Fare clic su **Salva** ed eseguire il processo.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Configurare Jenkins per distribuire l'app Web in Linux tramite Docker

Oltre a Git o FTP, l'app Web in Linux supporta la distribuzione tramite Docker. Per la distribuzione usando Docker, è necessario fornire un Dockerfile che includa l'app Web in runtime di servizio in un'immagine Docker. Il plug-in compila l'immagine, la inserisce in un registro Docker e la distribuisce nell'app Web.

Web App in Linux supporta anche modalità tradizionali come Git e FTP, ma solo per i linguaggi predefiniti come .NET Core, Node.js, PHP e Ruby. Per altri linguaggi, è necessario creare un pacchetto del runtime di servizio e del codice dell'applicazione in un'immagine docker e usare docker per la distribuzione.

Prima di configurare il processo in Jenkins, è necessario un servizio app di Azure su Linux. Per archiviare e gestire immagini del contenitore Docker private è necessario anche un registro contenitori. È possibile usare DockerHub; per questo esempio viene usato Registro contenitori di Azure.

* Seguire la procedura indicata [qui](../app-service/containers/quickstart-nodejs.md) per creare un'app Web in Linux. 
* Registro contenitori di Azure è un servizio gestito di [registri Docker] (https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry versione 2.0. Seguire la procedura [qui] (/azure/container-registry/container-registry-get-started-azure-cli) per altro materiale sussidiario utile per eseguire questa operazione. È anche possibile usare DockerHub.

### <a name="to-deploy-using-docker"></a>Per eseguire la distribuzione usando Docker:

1. Creare un nuovo progetto freestyle nel dashboard di Jenkins.
2. Configurare la **gestione del codice sorgente** da usare nel fork locale di [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) fornendo l'**URL del repository**. Ad esempio: http://github.com/&lt;yourID>/javawebappsample.
Aggiungere un'istruzione di compilazione per compilare il progetto usando Maven. A tale scopo aggiungere **Execute shell** e aggiungere la riga seguente in **Command**:    
```bash
mvn clean package
```

3. Aggiungere un'azione post-compilazione selezionando **Publish an Azure Web App** (Pubblica un'App Web di Azure).
4. Fornire **mySp**, l'entità servizio di Azure archiviata nel passaggio precedente come Azure Credentials.
5. Nella sezione **App Configuration** (Configurazione app) scegliere il gruppo di risorse e un'app Web di Linux nella sottoscrizione.
6. Scegliere Publish via Docker (Pubblica tramite Docker).
7. Compilare il percorso **Dockerfile**. È possibile mantenere il valore predefinito "/Dockerfile" per l'**URL del registro Docker**, specificare il formato https://&lt;myRegistry >.azurecr.io se si usa il Registro contenitori di Azure. Non specificare un valore se si usa DockerHub.
8. Per **Registry credentials** (Credenziali registro) aggiungere le credenziali per il Registro contenitori di Azure. Eseguire i comandi seguenti nell'interfaccia della riga di comando di Azure per ottenere l'ID utente e la password. Il primo comando abilita l'account Administrator.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. Il nome e il tag dell'immagine docker nella scheda **Avanzata** sono facoltativi. Per impostazione predefinita, il nome dell'immagine viene ottenuto dal nome dell'immagine configurata nel portale di Azure, nell'impostazione del contenitore Docker. Il tag viene generato da $BUILD_NUMBER. Assicurarsi di specificare il nome dell'immagine nel portale di Azure o fornire un valore per l'**immagine Docker** nella scheda **Avanzata**. Per questo esempio, specificare "&lt;yourRegistry >.azurecr.io/calculator" per **immagine Docker** e lasciare vuoto il valore del **tag immagine Docker**.
10. Si noti che la distribuzione avrà esito negativo se si usa l'impostazione dell'immagine Docker predefinita. Assicurarsi di modificare la configurazione di Docker in modo da usare l'immagine personalizzata nell'impostazione del contenitore Docker nel portale di Azure. Per l'immagine predefinita, usare il metodo di caricamento file per la distribuzione.
11. Analogamente all'approccio di caricamento di file, è possibile scegliere un slot diverso da quello di produzione.
12. Salvare e compilare il progetto. Viene eseguito il push dell'immagine del contenitore nel registro e l'app Web viene distribuita.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Eseguire la distribuzione nell'app Web in Linux tramite Docker usando la pipeline Jenkins

1. Nell'interfaccia utente Web di GitHub aprire il file **Jenkinsfile_container_plugin**. Fare clic sull'icona della matita per modificare il file aggiornando il gruppo di risorse e il nome dell'app Web rispettivamente nella riga 11 e 12.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Modificare la riga 13 sul server del registro contenitori    
```java
def registryServer = '<registryURL>'
```    

3. Modificare la riga 16 per aggiornare l'ID delle credenziali nell'istanza di Jenkins    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Creare una pipeline Jenkins    

1. Aprire Jenkins in un Web browser, fare clic su **New Item**.
2. Specificare un nome per il processo e selezionare **Pipeline**. Fare clic su **OK**.
3. Quindi fare clic sulla scheda **Pipeline**.
4. Per **Definition** selezionare **Pipeline script from SCM**.
5. Per **SCM** selezionare **Git**.
6. Immettere l'URL di GitHub per il repository con fork: https:&lt;your forked repo>.git</li>
7, Aggiornare **Script Path** in "Jenkinsfile_container_plugin"
8. Fare clic su **Salva** ed eseguire il processo.

## <a name="verify-your-web-app"></a>Verificare l'app Web

1. Per verificare che il file WAR sia stato distribuito correttamente nell'app Web. Aprire un Web browser.
2. Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Verrà visualizzato    
     un messaggio di benvenuto nell'app Web Java indicante che l'aggiornamento è stato eseguito.
   Sun Jun 17 16:39:10 UTC 2017
3. Passare a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sostituire &lt;x> e &lt;y> con numeri qualsiasi) per ottenere la somma di x e y        
    ![Calcolatrice: aggiungi](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Per il servizio app in Linux

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

In questa esercitazione usare il plug-in Servizio app di Azure per eseguire la distribuzione in Azure.

Si è appreso come:

> [!div class="checklist"]
> * Configurare Jenkins per distribuire il Servizio app di Azure tramite FTP 
> * Configurare Jenkins per eseguire la distribuzione nel Servizio app di Azure su Linux tramite Docker 
