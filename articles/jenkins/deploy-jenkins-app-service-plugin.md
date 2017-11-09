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
ms.openlocfilehash: e38c69ec55d894053792fbf284d07944d7f44dc0
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Eseguire la distribuzione nel Servizio app di Azure con il plug-in Jenkins 

Per distribuire un'app Web Java in Azure, è possibile usare l'interfaccia della riga di comando di Azure nella [pipeline Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) oppure il [plug-in Jenkins Servizio app di Azure](https://plugins.jenkins.io/azure-app-service). Il plug-in Jenkins versione 1.0 di supporta la distribuzione continua tramite la funzionalità App Web del Servizio app di Azure tramite:
* GIT e FTP.
* Docker per App Web in Linux.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare Jenkins per distribuire app Web tramite GIT e FTP.
> * Configurare Jenkins per distribuire app Web per contenitori.

## <a name="create-and-configure-a-jenkins-instance"></a>Creare e configurare un'istanza di Jenkins

Se non è già disponibile un master Jenkins, iniziare con il [modello di soluzione](install-jenkins-solution-template.md) che include Java Development Kit (JDK) versione 8 e i plug-in Jenkins necessari seguenti:

* [Plug-in Git client Jenkins](https://plugins.jenkins.io/git-client) versione 2.4.6 
* [Plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) versione 1.4.0
* [Credenziali di Azure](https://plugins.jenkins.io/azure-credentials) versione 1.2
* [Servizio app di Azure](https://plugins.jenkins.io/azure-app-server) versione 0.1

Si può usare il plug-in Jenkins per distribuire un'app Web in qualsiasi linguaggio supportato da App Web, ad esempio C#, PHP, Java e Node.js. In questa esercitazione viene usata l'app di esempio [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Per creare il fork del repository nel proprio account GitHub, selezionare il pulsante **Fork** nell'angolo superiore destro dell'interfaccia GitHub.  
> [!NOTE]
> Per compilare il progetto Java sono necessari Java JDK e Maven. Installare i componenti nel master Jenkins o nell'agente di macchine virtuali, se in uso per l'integrazione continua. 

Per installare i componenti, accedere all'istanza di Jenkins usando SSH ed eseguire i comandi seguenti:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Per eseguire la distribuzione in app Web per contenitori, installare Docker nel master Jenkins o nell'agente di macchine virtuali usato per la compilazione. Per istruzioni sull'installazione, vedere la guida all'[installazione di Docker su Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Aggiungere un'entità servizio di Azure alle credenziali di Jenkins

Per la distribuzione in Azure è necessaria un'entità servizio di Azure. 


1. Per creare un'entità servizio di Azure, usare l'[interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) o il [portale di Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Nel dashboard di Jenkins selezionare **Credentials** > **System** (Credenziali > Sistema). Quindi, selezionare **Global credentials(unrestricted)** (Credenziali globali senza restrizioni).
3. Per aggiungere un'entità servizio di Microsoft Azure, selezionare **Add Credentials** (Aggiungi credenziali). Immettere i valori per i campi relativi a **ID sottoscrizione**, **ID client**, **segreto client** e **endpoint di token OAuth 2.0**. Impostare il campo **ID** su **mySp**. Questo ID verrà usato nei passaggi successivi di questo articolo.


## <a name="configure-jenkins-to-deploy-web-apps-through-git-and-ftp"></a>Configurare Jenkins per distribuire app Web tramite GIT e FTP

Per distribuire il progetto in App Web, è possibile caricare gli artefatti di compilazione, ad esempio un file War in Java, usando Git o FTP.

Prima di configurare il processo in Jenkins sono necessari un piano di servizio app di Azure e un'app Web per l'esecuzione dell'app Java.


1. Creare un piano di servizio app di Azure con il piano tariffario **GRATUITO** usando il [comando `az appservice plan create` dell'interfaccia della riga di comando di Azure](/cli/azure/appservice/plan#create). Il piano di servizio app definisce le risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono queste risorse. Le risorse condivise permettono di risparmiare sui costi quando si ospitano più app.
2. Creare un'app Web. È possibile usare il [portale di Azure](/azure/app-service-web/web-sites-configure) o il comando `az` dell'interfaccia della riga di comando di Azure seguente:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Impostare la configurazione del runtime Java di cui l'app necessita. Il comando dell'interfaccia della riga di comando di Azure seguente configura l'app Web per l'esecuzione in un'istanza recente di JDK 8 JDK e in [Apache Tomcat](http://tomcat.apache.org/) versione 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Impostare il processo Jenkins

1. Creare un nuovo progetto **freestyle** nel dashboard di Jenkins.
2. Configurare il campo **Source Code Management** (Gestione del codice sorgente) in modo da usare il fork locale di [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Fornire l'**URL del repository**. Ad esempio: http://github.com/&lt;ID >/esempioappwebjava.
3. Aggiungere un passaggio per compilare il progetto con Maven aggiungendo il comando **Execute shell** (Esegui shell). Per questo esempio, è necessario un passaggio aggiuntivo per rinominare il file \*.war nella cartella di destinazione in **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Aggiungere un'azione post-compilazione selezionando **Publish an Azure Web App** (Pubblica un'App Web di Azure).
5. Inserire **mySp** come entità servizio di Azure. Questa entità servizio è stata archiviata come [Credenziali di Azure](#service-principal) in un passaggio precedente.
6. Nella sezione **App Configuration** (Configurazione app) scegliere l'app Web e il gruppo di risorse nella sottoscrizione. Il plug-in Jenkins rileva automaticamente se l'app Web è basata su Windows o Linux. Per un'app Web basata su Windows, viene visualizzata l'opzione **Publish Files** (Pubblica file).
7. Compilare i file che si vogliono distribuire. Se si usa Java, ad esempio, specificare il pacchetto WAR. Usare i parametri facoltativi **Source Directory** (Directory di origine) e **Target Directory** (Directory di destinazione) per specificare le cartelle di origine e di destinazione da usare per il caricamento dei file. L'app Web Java in Azure viene eseguita in un server Tomcat. Per Java, pertanto, si carica il pacchetto war nella cartella webapps. Per questo esempio, impostare il valore **Source Directory** (Directory di origine) su **target** e il valore **Target Directory** (Directory di destinazione) su **webapps**.
8. Se si vuole eseguire la distribuzione in uno slot di diverso dalla produzione, è possibile anche impostare il nome dello **Slot**.
9. Salvare e compilare il progetto. L'app web viene distribuita in Azure al termine della compilazione.

### <a name="deploy-web-apps-through-ftp-by-using-jenkins-pipeline"></a>Distribuire app Web tramite FTP usando la pipeline Jenkins

Il plug-in Jenkins Servizio app di Azure è pronto per la pipeline. È possibile fare riferimento all'esempio seguente nel repository GitHub.

1. Nell'interfaccia GitHub aprire il file **Jenkinsfile_ftp_plugin**. Per modificare il file, selezionare l'icona della matita. Aggiornare le definizioni **resourceGroup** e **webAppName** per le app Web rispettivamente nelle righe 11 e 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Impostare la definizione **withCredentials** nella riga 14 sull'ID delle credenziali nell'istanza di Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Creare una pipeline Jenkins

1. Aprire Jenkins in un Web browser. Selezionare **Nuovo elemento**.
2. Specificare un nome per il processo e selezionare **Pipeline**. Selezionare **OK**.
3. Selezionare la scheda **Pipeline**.
4. Per il valore **Definition** (Definizione) selezionare **Pipeline script from SCM** (Script di pipeline da SCM).
5. Per il valore **SCM** selezionare **Git**. Immettere l'URL di GitHub per il repository con fork. Ad esempio: https://&lt;repository_con_fork>.git.
6. Aggiornare il valore **Script Path** (Percorso script) in **Jenkinsfile_ftp_plugin**.
7. Selezionare **Save** (Salva) ed eseguire il processo.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configurare Jenkins per distribuire app Web per contenitori

App Web in Linux supporta la distribuzione tramite Docker. Per distribuire l'app Web usando Docker, è necessario fornire un Dockerfile che includa l'app Web con un runtime di servizio in un'immagine Docker. Il plug-in Jenkins compila l'immagine, la inserisce in un registro Docker e la distribuisce nell'app Web.

Web App in Linux supporta anche metodi di distribuzione tradizionali come Git e FTP, ma solo per i linguaggi predefiniti come .NET Core, Node.js, PHP e Ruby. Per altri linguaggi, è necessario creare un pacchetto del runtime di servizio e del codice dell'applicazione in un'immagine Docker e usare Docker per la distribuzione.

Prima di configurare il processo in Jenkins, è necessaria un'app Web in Linux. Per archiviare e gestire immagini del contenitore Docker private è necessario anche un registro contenitori. Per creare il registro contenitori si può usare DockerHub. In questo esempio viene usato il Registro contenitori di Azure.

* [Creare l'app Web in Linux](../app-service/containers/quickstart-nodejs.md).
* Registro contenitori di Azure è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry 2.0. [Creare un registro contenitori di Azure](/azure/container-registry/container-registry-get-started-azure-cli). È anche possibile usare DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Impostare il processo Jenkins per Docker

1. Creare un nuovo progetto **freestyle** nel dashboard di Jenkins.
2. Configurare il campo **Source Code Management** (Gestione del codice sorgente) in modo da usare il fork locale di [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Fornire l'**URL del repository**. Ad esempio: http://github.com/&lt;ID >/esempioappwebjava.
3. Aggiungere un passaggio per compilare il progetto con Maven aggiungendo un comando **Execute shell** (Esegui shell). Includere la riga seguente nel comando:
    ```bash
    mvn clean package
    ```

4. Aggiungere un'azione post-compilazione selezionando **Publish an Azure Web App** (Pubblica un'App Web di Azure).
5. Inserire **mySp** come entità servizio di Azure. Questa entità servizio è stata archiviata come [Credenziali di Azure](#service-principal) in un passaggio precedente.
6. Nella sezione **App Configuration** (Configurazione app) scegliere il gruppo di risorse e un'app Web di Linux nella sottoscrizione.
7. Scegliere **Publish via Docker** (Pubblica tramite Docker).
8. Compilare il valore del percorso **Dockerfile**. È possibile mantenere il valore predefinito /Dockerfile.
Per il valore **Docker registry URL** (URL registro Docker) immettere l'URL nel formato https://&lt;yourRegistry>.azurecr.io se si usa il Registro contenitori di Azure. Se si usa DockerHub, lasciare vuoto il valore.
9. Per il valore **Registry credentials** (Credenziali registro) aggiungere le credenziali per il registro contenitori. Eseguire i comandi seguenti nell'interfaccia della riga di comando di Azure per ottenere l'ID utente e la password. Il primo comando abilita l'account Administrator:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Il nome e il valore tag dell'immagine Docker nella scheda **Advanced** (Avanzate) sono facoltativi. Per impostazione predefinita, il valore per il nome dell'immagine viene ottenuto dal nome dell'immagine configurata nel portale di Azure, nell'impostazione **Contenitore Docker**. Il tag viene generato da $BUILD_NUMBER.
    > [!NOTE]
    > Assicurarsi di specificare il nome dell'immagine nel portale di Azure o fornire un valore **Docker Image** (Immagine Docker)nella scheda **Advanced** (Avanzate). Per questo esempio, impostare il valore dell'**immagine Docker** su &lt;yourRegistry >.azurecr.io/calculator e lasciare vuoto il valore **Docker Image Tag** (Tag immagine Docker).

11. La distribuzione avrà esito negativo se si usa l'impostazione dell'immagine Docker predefinita. Modificare la configurazione di Docker in modo da usare un'immagine personalizzata nell'impostazione **Contenitore Docker** nel portale di Azure. Per l'immagine predefinita, usare il metodo di caricamento file per la distribuzione.
12. Analogamente all'approccio di caricamento di file, è possibile scegliere un nome **Slot** diverso da quello di **produzione**.
13. Salvare e compilare il progetto. Viene eseguito il push dell'immagine del contenitore nel registro e l'app Web viene distribuita.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Distribuire app Web per contenitori tramite la pipeline Jenkins

1. Nell'interfaccia GitHub aprire il file **Jenkinsfile_container_plugin**. Per modificare il file, selezionare l'icona della matita. Aggiornare le definizioni **resourceGroup** e **webAppName** per le app Web rispettivamente nelle righe 11 e 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Modificare la riga 13 sul server del registro contenitori:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Modificare la riga 16 per usare l'ID delle credenziali nell'istanza di Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Creare una pipeline Jenkins    

1. Aprire Jenkins in un Web browser. Selezionare **Nuovo elemento**.
2. Specificare un nome per il processo e selezionare **Pipeline**. Selezionare **OK**.
3. Selezionare la scheda **Pipeline**.
4. Per il valore **Definition** (Definizione) selezionare **Pipeline script from SCM** (Script di pipeline da SCM).
5. Per il valore **SCM** selezionare **Git**. Immettere l'URL di GitHub per il repository con fork. Ad esempio: https://&lt;repository_con_fork>.git.
7. Aggiornare il valore **Script Path** (Percorso script) in **Jenkinsfile_container_plugin**.
8. Selezionare **Save** (Salva) ed eseguire il processo.

## <a name="verify-your-web-app"></a>Verificare l'app Web

1. Per verificare che il file WAR sia stato distribuito correttamente nell'app Web, aprire un Web browser.
2. Passare a http://&lt;nome_app>.azurewebsites.net/api/calculator/ping. Sostituire &lt;nome_app> con il nome dell'app Web. Viene visualizzato il messaggio:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Passare a http://&lt;nome_app>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Sostituire &lt;x> e &lt;y> con numeri qualsiasi per ottenere la somma di x e y. La calcolatrice mostra la somma: ![Calcolatrice: aggiungi](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Per Servizio app di Azure in Linux

1. Per verificare l'app Web, eseguire il comando seguente nell'interfaccia della riga di comando di Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Viene visualizzato il messaggio seguente:
    ```CLI
    ["calculator"]
    ```
    
2. Passare a http://&lt;nome_app>.azurewebsites.net/api/calculator/ping. Sostituire &lt;nome_app> con il nome dell'app Web. Viene visualizzato il messaggio: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Passare a http://&lt;nome_app>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Sostituire &lt;x> e &lt;y> con numeri qualsiasi per ottenere la somma di x e y.
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il plug-in Jenkins Servizio app di Azure per eseguire la distribuzione in Azure.

Si è appreso come:

> [!div class="checklist"]
> * Configurare Jenkins per distribuire il Servizio app di Azure tramite FTP 
> * Configurare Jenkins per la distribuzione in app Web per contenitori 