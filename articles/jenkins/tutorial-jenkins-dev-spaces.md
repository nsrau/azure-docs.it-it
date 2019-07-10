---
title: Uso del plug-in Azure Dev Spaces per Jenkins con il servizio Azure Kubernetes
description: Informazioni su come usare il plug-in Azure Dev Spaces nella pipeline di integrazione continua.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/19
ms.openlocfilehash: 49d4e56dabf046675970c65f2be71cea5094e9ef
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550870"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Esercitazione: Uso del plug-in Azure Dev Spaces per Jenkins con il servizio Azure Kubernetes 

Azure Dev Spaces consente di testare e sviluppare in modo iterativo un'applicazione di microservizi in esecuzione nel servizio Azure Kubernetes senza la necessità di replicare o simulare dipendenze. Il plug-in Azure Dev Spaces per Jenkins consente di usare Dev Spaces nella pipeline di integrazione continua e distribuzione continua (CI/CD).

In questa esercitazione viene usato anche il servizio Registro Azure Container. Questo servizio archivia le immagini e una delle relative attività crea artefatti Docker e Helm. Usando il Registro Azure Container e la relativa attività per la generazione di artefatti, è possibile evitare di installare software aggiuntivo, come Docker, nel server Jenkins. 

In questa esercitazione si completeranno le attività seguenti:

> [!div class="checklist"]
> * Creare un cluster del servizio Azure Kubernetes abilitato per Azure Dev Spaces
> * Distribuire un'applicazione multiservizio nel servizio Azure Kubernetes
> * Preparare il server Jenkins
> * Usare il plug-in Azure Dev Spaces in una pipeline Jenkins per visualizzare in anteprima le modifiche del codice prima di unirle nel progetto

Questa esercitazione presuppone una conoscenza di livello intermedio dei principali servizi di Azure, ossia Azure Kubernetes, Registro Azure Container, Azure Dev Spaces, [pipeline](https://jenkins.io/doc/book/pipeline/) e plug-in Jenkins e GitHub. È utile avere una familiarità di base con gli strumenti di supporto come kubectl e Helm.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Un account GitHub. Se non si ha un account GitHub, crearne [uno gratuito](https://github.com/) prima di iniziare.

* [Visual Studio Code](https://code.visualstudio.com/download) con l'estensione [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installata.

* L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.43 o superiore installata.

* Un server master Jenkins. Se non è già disponibile un server master Jenkins, distribuire [Jenkins](https://aka.ms/jenkins-on-azure) in Azure seguendo la procedura descritta in questo argomento di [avvio rapido](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Nel server Jenkins devono essere installati gli strumenti Helm e kubectl, che devono essere disponibili per l'account Jenkins, come descritto più avanti in questa esercitazione.

* VS Code, il terminale di VS Code o WSL e Bash. 


## <a name="create-azure-resources"></a>Creare le risorse di Azure

In questa sezione vengono create le risorse di Azure:

* Un gruppo di risorse che conterrà tutte le risorse di Azure per questa esercitazione.
* Un cluster del [servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/).
* Un [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) per compilare (tramite Attività del Registro Azure Container) e archiviare immagini Docker.

1. Creare un gruppo di risorse.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Creare un cluster del servizio Azure Kubernetes. Creare il cluster del servizio Azure Kubernetes in un'[area che supporta Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configurare il servizio Azure Kubernetes per l'uso di Dev Spaces.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    In questo passaggio viene installata l'estensione `azds` dell'interfaccia della riga di comando.

4. Creare un registro contenitori.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Distribuire app di esempio nel cluster del servizio Azure Kubernetes

In questa sezione viene configurata un'istanza di Dev Spaces e viene distribuita un'applicazione di esempio nel cluster del servizio Azure Kubernetes creato nella sezione precedente. L'applicazione è costituita da due parti, *webfrontend* e *mywebapi*. Entrambi i componenti vengono distribuiti in un'istanza di Dev Spaces. Più avanti in questa esercitazione verrà inviata una richiesta pull per mywebapi per attivare la pipeline CI in Jenkins.

Per altre informazioni sull'uso di Azure Dev Spaces e sullo sviluppo multiservizio con Azure Dev Spaces, vedere [Guida introduttiva ad Azure Dev Spaces con Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java) e [Sviluppo multiservizio con Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Queste esercitazioni forniscono altre informazioni generali non incluse in questo articolo.

1. Scaricare il repository https://github.com/Azure/dev-spaces da GitHub.

2. Aprire la cartella `samples/java/getting-started/webfrontend` in VS Code. Le richieste, visualizzate per impostazione predefinita, di aggiungere risorse di debug o di ripristinare il progetto possono essere ignorate.

3. Aggiornare `/src/main/java/com/ms/sample/webfrontend/Application.java` in questo modo:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Fare clic su **Visualizza** e quindi su **Terminale** per aprire il terminale integrato di VS Code.

5. Eseguire il comando `azds prep` per preparare l'applicazione per l'esecuzione in un'istanza di Dev Spaces. Per preparare correttamente l'applicazione, questo comando deve essere eseguito da `dev-spaces/samples/java/getting-started/webfrontend`:

    ```bash
    azds prep --public
    ```

    Il comando `azds prep` dell'interfaccia della riga di comando di Dev Spaces genera gli asset Docker e Kubernetes con le impostazioni predefinite. Questi file vengono mantenuti per l'intera durata del progetto e possono essere personalizzati:

    * `./Dockerfile` e `./Dockerfile.develop` descrivono l'immagine del contenitore dell'app e come viene compilato ed eseguito il codice sorgente all'interno del contenitore.
    * Un [grafico Helm](https://helm.sh/docs/developing_charts/) in `./charts/webfrontend` descrive come distribuire il contenitore a Kubernetes.
    * `./azds.yaml` è il file di configurazione di Azure Dev Spaces.

    Per altre informazioni, vedere [Funzionamento e configurazione di Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Compilare ed eseguire l'applicazione nel servizio Azure Kubernetes usando il comando `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Analizzare l'output della console per informazioni sull'URL creato con il comando `up`. Sarà nel formato:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Aprire questo URL in una finestra del browser per visualizzare l'app Web. Durante l'esecuzione del contenitore, l'output `stdout` e `stderr` vengono trasmessi nella finestra del terminale.

8. Quindi, configurare e distribuire *mywebapi*:

    1. Passare alla directory `dev-spaces/samples/java/getting-started/mywebapi`

    2. Esegui

        ```bash
        azds prep
        ```

    3. Esegui

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Preparare il server Jenkins

In questa sezione il server Jenkins viene preparato per l'esecuzione della pipeline CI di esempio.

* Installare i plug-in
* Installare l'interfaccia della riga di comando di Helm e Kubernetes
* Aggiungere le credenziali

### <a name="install-plugins"></a>Installare i plug-in

1. Accedere al server Jenkins. Scegliere **Gestisci Jenkins > Gestisci plug-in**.
2. Nella scheda **Disponibili** selezionare questi plug-in:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Attività del Registro Azure Container](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject)
    * [Integrazione GitHub](https://plugins.jenkins.io/github-pullrequest)

    Se questi plug-in non sono inclusi nell'elenco, passare alla scheda **Installati** per verificare se sono già installati.

3. Per installare i plug-in, scegliere **Download now and install after restart** (Scarica subito e installa dopo il riavvio).

4. Riavviare il server Jenkins per completare l'installazione.

### <a name="install-helm-and-kubectl"></a>Installare Helm e kubectl

La pipeline di esempio usa Helm e kubectl per eseguire la distribuzione nell'istanza di Dev Spaces. Quando viene installato, Jenkins crea un account amministratore denominato *jenkins*. Helm e kubectl devono essere accessibili all'utente jenkins.

1. Stabilire una connessione SSH con il server master Jenkins. 

2. Passare all'utente `jenkins`:
    ```bash
    sudo su jenkins
    ```

3. Installare l'interfaccia della riga di comando di Helm. Per altre informazioni, vedere [Installazione di Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Installare kubectl. Per altre informazioni, vedere [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Aggiungere le credenziali in Jenkins

1. Jenkins richiede un'entità servizio di Azure per l'autenticazione e l'accesso alle risorse di Azure. Per creare l'entità servizio, vedere la sezione [Creare l'entità servizio](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) dell'esercitazione Eseguire la distribuzione in Servizio app di Azure. Assicurarsi di salvare una copia dell'output di `create-for-rbac` perché queste informazioni saranno necessarie per completare il passaggio successivo. Verrà visualizzato un risultato simile al seguente:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Usando le informazioni dell'entità servizio del passaggio precedente, aggiungere un tipo di credenziale *Microsoft Azure Service Principal* (Entità servizio di Microsoft Azure) in Jenkins. I nomi dello screenshot seguente corrispondono all'output di `create-for-rbac`.

    Il campo **ID** corrisponde al nome delle credenziali di Jenkins per l'entità servizio. Nell'esempio viene usato il valore di `displayName` (in questo caso `xxxxxxxjenkinssp`), ma è possibile usare qualsiasi testo. Il nome delle credenziali corrisponde al valore della variabile di ambiente AZURE_CRED_ID indicata nella sezione successiva.

    ![Aggiungere le credenziali dell'entità servizio in Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    La **descrizione** è facoltativa. Per istruzioni più dettagliate, vedere la sezione[Aggiungere l'entità servizio a Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) dell'esercitazione Eseguire la distribuzione in Servizio app di Azure. 



3. Per visualizzare le credenziali del Registro Azure Container, eseguire questo comando:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Creare una copia dell'output JSON, che dovrebbe essere simile al seguente:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Aggiungere un tipo di credenziale *Username with password* (Nome utente e password) in Jenkins. Il **nome utente** è quello del passaggio precedente, in questo esempio `acr01`. La **password** corrisponde al valore della prima password, in questo esempio `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. L'**ID** di queste credenziali corrisponde al valore di ACR_CRED_ID.

5. Configurare le credenziali del servizio Azure Kubernetes. Aggiungere un tipo di credenziale *Kubernetes configuration (kubeconfig)* (Configurazione di Kubernetes) in Jenkins. Usare l'opzione "Enter directly" (Immetti direttamente). Per ottenere le credenziali di accesso per il cluster del servizio Azure Kubernetes, eseguire il comando seguente:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   L'**ID** di queste credenziali corrisponde al valore di KUBE_CONFIG_ID nella sezione successiva.

## <a name="create-a-pipeline"></a>Creare una pipeline

Lo scenario selezionato per la pipeline di esempio si basa su un modello reale: una richiesta pull attiva una pipeline CI che crea e quindi distribuisce le modifiche proposte in un'istanza di Azure Dev Spaces per i test e la revisione. In base al risultato della revisione, le modifiche vengono unite e distribuite nel servizio Azure Kubernetes oppure vengono rimosse. Infine, l'istanza di Dev Spaces viene rimossa.

La configurazione della pipeline Jenkins e Jenkinsfile definiscono le fasi della pipeline CI. Questo diagramma di flusso illustra le fasi della pipeline e i punti di decisione definiti dal Jenkinsfile:

![Flusso della pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Scaricare una versione modificata del progetto *mywebapi* da https://github.com/azure-devops/mywebapi. Questo progetto contiene diversi file necessari per creare una pipeline, inclusi il *Jenkinsfile*, i *Dockerfile* e il grafico Helm.

2. Accedere a Jenkins. Scegliere **Add Item** (Aggiungi elemento) dal menu a sinistra.

3. Selezionare **Pipeline** e quindi immettere un nome nella casella **Enter an item name** (Immettere un nome di elemento). Selezionare **OK** per visualizzare automaticamente la schermata di configurazione della pipeline.

4. Nella scheda **General** (Generale) selezionare **Prepare an environment for the run** (Preparare un ambiente per l'esecuzione). 

5. Selezionare le opzioni **Keep Jenkins Environment Variables** (Mantieni le variabili di ambiente Jenkins) e **Keep Jenkins Build Variables** (Mantieni le variabili di compilazione Jenkins).

6. Nella casella **Properties Content** (Contenuto proprietà) immettere le variabili di ambiente seguenti:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Se si usano i valori di esempio forniti nelle sezioni precedenti, l'elenco di variabili di ambiente sarà simile a questo:

    ![Variabili di ambiente della pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Scegliere**Pipeline script from SCM** (Script di pipeline da SCM) in **Pipeline > Definition** (Pipeline > Definizione).
8. In **SCM** scegliere **Git** e quindi immettere l'URL del repository.
9. In **Branch Specifier** (Identificatore ramo) immettere `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Compilare l'URL del repository SCM e il percorso dello script "Jenkinsfile".
11. L'opzione **Lightweight checkout** (Estrazione leggera) dovrebbe essere selezionata.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Creare una richiesta pull per attivare la pipeline

Per completare il passaggio 3 di questa sezione, è necessario impostare come commento parte del Jenkinsfile, altrimenti si riceve un errore 404 quando si prova a visualizzare la nuova versione e quella precedente affiancate. Per impostazione predefinita, quando si sceglie di unire la richiesta pull, la versione condivisa precedente di mywebapi verrà rimossa e sostituita da quella nuova. Apportare la modifica seguente nel Jenkinsfile prima di completare il passaggio 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Apportare una modifica in `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java` e quindi creare una richiesta pull. Ad esempio:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Accedere a Jenkins e selezionare il nome della pipeline, quindi scegliere **Build Now** (Compila ora). 

    È anche possibile configurare un *webhook* per attivare automaticamente la pipeline Jenkins. Quando viene immessa una richiesta pull, GitHub invia un comando POST a Jenkins, attivando la pipeline. Per altre informazioni sulla configurazione di un webhook, vedere [Connettere Jenkins a GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Confrontare le modifiche con la versione condivisa corrente:

    1. Aprire il browser e passare alla versione condivisa `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contiene l'URL.

    2. Aprire un'altra scheda e quindi immettere l'URL dell'istanza di Dev Spaces della richiesta pull. Sarà analogo a `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Il collegamento è disponibile in **Build History > <build#> > Console Output** (Cronologia compilazione > <n. compilazione> > Output console) per il processo di Jenkins. Cercare `aksapp` nella pagina oppure, per visualizzare solo il prefisso, cercare `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Creazione dell'URL dell'istanza figlio di Dev Spaces

Quando si immette una richiesta pull, Jenkins crea un'istanza figlio di Dev Spaces basata su quella condivisa del team ed esegue il codice della richiesta pull in tale istanza figlio. L'URL dell'istanza figlio di Dev Spaces ha il formato `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** viene impostato durante l'esecuzione della pipeline dal plug-in Azure Dev Spaces tramite **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` è l'URL dell'app Web front-end distribuita in precedenza usando `azds up` nel [passaggio 7 della sezione Distribuire app di esempio nel cluster del servizio Azure Kubernetes](#test_endpoint). Il valore di `$env.TEST_ENDPOINT` viene impostato nella configurazione della pipeline. 

Il frammento di codice seguente mostra come viene usato l'URL dell'istanza figlio di Dev Spaces nella fase `smoketest`. Il codice verifica se l'istanza figlio TEST_ENDPOINT di Dev Spaces è disponibile e, in caso affermativo, scarica il messaggio di saluto in StdOut:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato l'applicazione di esempio, pulire le risorse di Azure eliminando il gruppo di risorse:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha decritto come usare il plug-in Azure Dev Spaces per Jenkins e il plug-in Registro Azure Container per compilare codice e distribuirlo in un'istanza di Dev Spaces.

Le risorse riportate nell'elenco seguente contengono altre informazioni su Azure Dev Spaces, Attività del Registro Azure Container e CI/CD con Jenkins.

Azure Dev Spaces:
* [Funzionamento e configurazione di Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Attività del Registro Azure Container:
* [Automate OS and framework patching with ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) (Automatizzare la correzione di sistema operativo e framework con le attività di Registro Azure Container)
* [Creazione automatica in caso di commit del codice](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD con Jenkins in Azure:
* [Distribuzione continua di Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
