---
title: Usare Jenkins per distribuire le app Web in Azure | Microsoft Docs
description: Impostare l'integrazione continua da GitHub in Servizio app di Azure per le app Web Java con Jenkins e Docker.
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 3a2635ac968d843226f05dc51cf4a5f078235c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Configurare l'integrazione e la distribuzione continue in Servizio App di Azure con Jenkins

Questa esercitazione consente di impostare l'integrazione e la distribuzione continue di un'app Web Java di esempio sviluppata con il framework [Spring Boot](http://projects.spring.io/spring-boot/) nell'[app Web di Servizio app di Azure in Linux](/azure/app-service/containers/app-service-linux-intro) con Jenkins.

In questa esercitazione si imparerà a:

> [!div class="checklist"]
> * Installare i plug-in Jenkins necessari per la distribuzione in Servizio app di Azure.
> * Definire un processo Jenkins per creare immagini Docker da un repository GitHub quando viene eseguito il push di un nuovo commit.
> * Definire una nuova app Web di Azure per Linux e configurarla per la distribuzione di immagini Docker sottoposte a push nel registro contenitori di Azure.
> * Configurare il plug-in Jenkins per Servizio app di Azure.
> * Distribuire l'app di esempio in Servizio app di Azure con una compilazione manuale.
> * Attivare una compilazione di Jenkins e aggiornare l'app Web mediante il push delle modifiche a GitHub.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questa esercitazione, sono necessari:

* [Jenkins](https://jenkins.io/) con gli strumenti JDK e Maven configurati. Se non si dispone di un sistema Jenkins, crearne uno ora in Azure dal [modello di soluzione Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Un account [GitHub](https://github.com).
* [Versione 2.0 dell'interfaccia della riga di comando di Azure](/cli/azure/overview) dalla riga di comando locale o in [Azure Cloud Shell](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Installare i plug-in Jenkins

1. Aprire un Web browser nella console Web di Jenkins e selezionare **Manage Jenkins** dal menu a sinistra, quindi scegliere **Manage Plugins**.
2. Selezionare la scheda **Available**.
3. Cercare e selezionare la casella di controllo accanto ai plug-in seguenti:   

    - [Plug-in per Servizio app di Azure](https://plugins.jenkins.io/azure-app-service)
    - [Pulg-in per GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)

    Se i plug-in non vengono visualizzati, verificare che non siano già installati controllando la scheda **Installed**.

1. Selezionare **Download now and install after restart** per abilitare i plug-nella configurazione di Jenkins.

## <a name="configure-github-and-jenkins"></a>Configurare GitHub e Jenkins

Impostare Jenkins per la ricezione di [webhook GitHub](https://developer.github.com/webhooks/) quando viene eseguito il push di nuovi commit in un repository dell'account.

1. Selezionare **Manage Jenkins**, quindi **Configure System**. Nella sezione **GitHub** verificare che **Manage hooks** sia selezionata e quindi scegliere **Manage additional GitHub actions** e infine **Convert login and password to token**.
2. Selezionare il pulsante di opzione **From login and password** e immettere il nome utente e la password GitHub. Selezionare **Create token credentials** per creare un nuovo [token di accesso personale GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Creare un token di accesso personale GitHub dall'account di accesso e dalla password](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Selezionare il token appena creato dal menu a discesa **Credentials** nella configurazione dei server di GitHub. Selezionare **Test connection** per verificare il funzionamento dell'autenticazione.   
   ![Verificare la connessione a GitHub dopo aver configurato un token di accesso personale](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Se nell'account GitHub è abilitata l'autenticazione a due fattori, creare il token in GitHub e configurarne l'uso da parte di Jenkins. Per i dettagli completi, consultare la documentazione dei [plug-in di GitHub per Jenkins](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Creare tramite fork una copia del repository di esempio e creare un processo Jenkins 

1. Aprire il [repository dell'applicazione Spring Boot di esempio](https://github.com/spring-guides/gs-spring-boot-docker) e crearne una copia tramite fork nell'account GitHub selezionando **Fork** nell'angolo superiore destro.   
    ![Creare una copia tramite fork da GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. Nella console Web di Jenkins selezionare **New Item**, denominarlo **MyJavaApp**, selezionare **Freestyle project**, quindi **OK**.   
    ![Nuovo progetto Freestyle Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Nella sezione **General** selezionare il progetto **GitHub** e immettere l'URL della copia del repository creata tramite fork, ad esempio https://github.com/raisa/gs-spring-boot-docker
3. Nella sezione **Source code management** selezionare **Git** e immettere l'URL `.git` della copia del repository creata tramite fork, ad esempio https://github.com/raisa/gs-spring-boot-docker.git
4. Nella sezione **Build Triggers** (Trigger di compilazione) selezionare **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm).
5. Nella sezione **Build** selezionare **Add build step** e scegliere **Invoke top-level Maven targets**. Immettere `package` nel campo **Obiettivi**.
6. Selezionare **Salva**. Per testare il processo, selezionare **Build Now** dalla pagina del progetto.

## <a name="configure-azure-app-service"></a>Configurare Servizio app di Azure 

1. Usando l'interfaccia della riga di comando di Azure o [Cloud Shell](/azure/cloud-shell/overview), creare una nuova [app Web in Linux](/azure/app-service/containers/app-service-linux-intro). Il nome dell'app Web in questa esercitazione è `myJavaApp`, ma è necessario usare un nome univoco per la propria app.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan
    ```

2. Creare un [registro contenitori di Azure](/azure/container-registry/container-registry-intro) per archiviare le immagini Docker create da Jenkins. Il nome del registro contenitori usato in questa esercitazione è `jenkinsregistry`, ma è necessario usare un nome univoco per il proprio registro contenitori. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configurare l'app Web per l'esecuzione delle immagini Docker sottoposte a push nel registro contenitori e specificare che l'app in esecuzione nel contenitore ascolta le richieste sulla porta 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Configurare il plug-in Jenkins per Servizio app di Azure

1. Nella console Web di Jenkins selezionare il processo **MyJavaApp** creato e quindi **Configure** nella parte sinistra della pagina.
2. Scorrere verso il basso fino a **Post-build Actions**, quindi selezionare **Add post-build action** e scegliere **Publish an Azure Web App**.
3. In **Azure Profile Configuration** selezionare **Add** accanto ad **Azure Credentials** e scegliere **Jenkins**.
4. Nella finestra di dialogo **Add Credentials** selezionare **Microsoft Azure Service Principal** dall'elenco a discesa **Kind**.
5. Creare un'entità servizio Active Directory dall'interfaccia della riga di comando di Azure o da [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Immettere le credenziali dall'entità servizio nella finestra di dialogo **Aggiungi credenziali**. Se non si conosce l'ID sottoscrizione di Azure, è possibile eseguire una query dall'interfaccia della riga di comando:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configurare un'entità servizio di Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Verificare che l'entità servizio esegua l'autenticazione con Azure selezionando **Verify Service Principal**. 
7. Selezionare **Add** per salvare le credenziali.
8. Selezionare le credenziali dell'entità servizio appena aggiunte dall'elenco a discesa **Azure Credentials** quando si è di nuovo nella configurazione **Publish an Azure Web App**.
9. In **App Configuration** scegliere il gruppo di risorse e il nome dell'app Web dall'elenco a discesa.
10. Selezionare il pulsante di opzione **Publish via Docker**.
11. Immettere `complete/Dockerfile` per il **percorso di Dockerfile**.
12. Immettere `https://jenkinsregistry.azurecr.io` nel campo **Docker registry URL**.
13. Selezionare **Add** accanto a **Registry Credentials**. 
14. Immettere il nome utente dell'amministratore per il registro contenitori di Azure creato per **Username**.
15. Immettere la password per il registro contenitori di Azure nel campo **Password**. È possibile ottenere il nome utente e la password dal portale di Azure o tramite il comando seguente dell'interfaccia della riga di comando:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Aggiungere le credenziali del registro contenitori](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Selezionare **Add** per salvare le credenziali.
16. Selezionare le credenziali appena create dall'elenco a discesa **Registry credentials** nel pannello **App Configuration** per **Publish an Azure Web App**. L'azione di post-compilazione completata avrà un aspetto simile all'immagine seguente:   
    ![Configurazione dell'azione di post-compilazione per la distribuzione di Servizio app di Azure](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Selezionare **Save** per salvare la configurazione del processo.

## <a name="deploy-the-app-from-github"></a>Distribuire l'app da GitHub

1. Nel progetto di Jenkins selezionare **Build Now** per distribuire l'app di esempio in Azure.
2. Al termine della compilazione l'app verrà pubblicata in Azure al relativo URL di pubblicazione, ad esempio http://myjavaapp.azurewebsites.net.   
   ![Visualizzare l'app distribuita in Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Eseguire il push delle modifiche e ripetere la ridistribuzione

1. Dal fork Github passare sul Web a `complete/src/main/java/Hello/Application.java`. Selezionare il collegamento **Edit this file** al lato destro dell'interfaccia GitHub.
2. Apportare la modifica seguente al metodo `home()` ed eseguirne il commit nel ramo master del repository.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Verrà avviata una nuova compilazione in Jenkins, attivata dal nuovo commit nel ramo `master` del repository. Al termine ricaricare l'app in Azure.     
      ![Visualizzare l'app distribuita in Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Passaggi successivi

- [Usare macchine virtuali di Azure come agenti di compilazione](/azure/jenkins/jenkins-azure-vm-agents)
- [Gestire le risorse in processi e pipeline con l'interfaccia della riga di comando di Azure](/azure/jenkins/execute-cli-jenkins-pipeline)
 
