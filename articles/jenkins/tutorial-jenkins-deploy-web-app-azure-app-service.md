---
title: 'Esercitazione: Eseguire la distribuzione da GitHub in Servizio app di Azure con Jenkins'
description: Configurare Jenkins per l'integrazione continua (CI) da GitHub e la distribuzione continua (CD) in Servizio app di Azure per le app Web Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: 90f89f9ffb1d55e7621c87f168375251c78d9730
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533494"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Esercitazione: Eseguire la distribuzione da GitHub in Servizio app di Azure con integrazione continua e distribuzione continua di Jenkins

Questa esercitazione distribuisce un'app Web Java di esempio da GitHub in [Servizio app di Azure in Linux](/azure/app-service/containers/app-service-linux-intro) configurando l'integrazione continua (CI) e la distribuzione continua (CD) in Jenkins. Quando si aggiorna l'app eseguendo il push di commit in GitHub, Jenkins compila automaticamente l'app e la pubblica di nuovo in Servizio app di Azure. L'app di esempio in questa esercitazione è stata sviluppata usando il framework [Spring Boot](https://projects.spring.io/spring-boot/). 

![Panoramica](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

In questa esercitazione si completeranno le attività seguenti:

> [!div class="checklist"]
> * Installare i plug-in Jenkins per eseguire la compilazione da GitHub, eseguire la distribuzione in Servizio app di Azure ed eseguire altre attività correlate.
> * Creare una copia tramite fork del repository GitHub di esempio in modo da disporre di una copia di lavoro.
> * Connettere Jenkins a GitHub.
> * Creare un'entità servizio di Azure in modo che Jenkins possa accedere ad Azure senza usare le credenziali dell'utente.
> * Aggiungere l'entità servizio a Jenkins.
> * Creare la pipeline Jenkins che compila e distribuisce l'app di esempio ogni volta che si aggiorna l'app in GitHub.
> * Creare file di compilazione e distribuzione per la pipeline Jenkins.
> * Puntare la pipeline Jenkins allo script di compilazione e distribuzione.
> * Distribuire l'app di esempio in Azure eseguendo una compilazione manuale.
> * Eseguire il push di un aggiornamento dell'app in GitHub, per fare in modo che Jenkins esegua la compilazione e la ridistribuzione in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario quanto segue:

* Un server [Jenkins](https://jenkins.io/) con Java Development Kit (JDK) e gli strumenti Maven installati in una macchina virtuale Linux di Azure

  Se non si ha un server Jenkins, completare questa procedura nel portale di Azure: [Creare un server Jenkins in una macchina virtuale Linux di Azure](/azure/jenkins/install-jenkins-solution-template)

* Un account [GitHub](https://github.com) per ottenere una copia di lavoro (fork) per l'app Web Java di esempio. 

* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli), che è possibile eseguire dalla riga di comando locale o da [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Installare i plug-in Jenkins

1. Accedere alla console Web Jenkins da questo percorso:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Nella pagina principale di Jenkins selezionare **Manage Jenkins** > **Manage Plugins** (Gestisci Jenkins > Gestisci plug-in).

   ![Gestire i plug-in Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Nella scheda **Available** (Disponibile) selezionare questi plug-in:

   - [Servizio app di Azure](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - [Environment Injector Plugin](https://plugins.jenkins.io/envinject) Jenkins
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   Se i plug-in non vengono visualizzati, verificare che non siano già installati controllando la scheda **Installed** (Installato).

1. Per installare i plug-in selezionati, scegliere **Download now and install after restart** (Scarica subito e installa dopo il riavvio).

1. Al termine, dal menu di Jenkins scegliere **Manage Jenkins** (Gestisci Jenkins) per tornare alla pagina di gestione di Jenkins per i passaggi successivi.

## <a name="fork-sample-github-repo"></a>Creare una copia tramite fork del repository GitHub di esempio

1. [Accedere al repository GitHub per l'app di esempio Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. Nell'angolo superiore destro in GitHub scegliere **Fork**.

   ![Creare una copia tramite fork del repository di esempio in GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Seguire le istruzioni per selezionare l'account GitHub e completare la creazione di una copia tramite fork.

Configurare quindi Jenkins con le proprie credenziali di GitHub.

## <a name="connect-jenkins-to-github"></a>Connettere Jenkins a GitHub

Per fare in modo che Jenkins monitori GitHub e risponda quando viene eseguito il push di nuovi commit nell'app Web nel fork di GitHub, abilitare i [webhook di GitHub](https://developer.github.com/webhooks/) in Jenkins.

> [!NOTE]
> 
> Questi passaggi creano credenziali di token di accesso personale per consentire a Jenkins di usare GitHub tramite nome utente e password di GitHub dell'utente. 
> Se tuttavia l'account GitHub usa l'autenticazione a due fattori, è necessario creare il token in GitHub e configurare Jenkins per usare tale token. 
> Pe altre informazioni, vedere la documentazione del [plug-in di GitHub per Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. Nella pagina **Manage Jenkins** (Gestisci Jenkins) selezionare **Configure System** (Configura sistema). 

   ![Configurare il sistema](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Nella sezione **GitHub** specificare i dettagli per il server GitHub. Nell'elenco **Add GitHub Server** (Aggiungi server GitHub) selezionare **GitHub Server** (Server GitHub). 

   ![Aggiungere il server GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Se la proprietà **Manage hooks** (Gestisci hook) non è selezionata, selezionarla. Scegliere **Advanced** (Avanzate) per specificare altre impostazioni. 

   ![Scegliere "Advanced" (Avanzate) per altre impostazioni](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Nell'elenco **Manage additional GitHub actions** (Gestisci azioni GitHub aggiuntive) selezionare **Convert login and password to token** (Converti account di accesso e password in token).

   ![Scegliere "Manage additional GitHub actions" (Gestisci azioni GitHub aggiuntive)](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Selezionare **From login and password** (Da account di accesso e password) e immettere il nome utente e la password di GitHub. Al termine, scegliere **Create token credentials** (Crea credenziali token) per creare un [token di accesso personale di GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Creare un token di accesso personale di GitHub da account di accesso e password](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Nell'elenco **Credentials** (Credenziali) della sezione **GitHub Server** (Server GitHub) selezionare il nuovo token. Per controllare che l'autenticazione funzioni, scegliere **Test connection** (Test connessione).

   ![Controllare la connessione al server GitHub con il nuovo token di accesso personale](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Creare quindi l'entità servizio di Azure usata da Jenkins per l'autenticazione e l'accesso alle risorse di Azure.

## <a name="create-service-principal"></a>Creare un'entità servizio

In una sezione successiva si creerà un processo di pipeline Jenkins che compila l'app da GitHub e la distribuisce in Servizio app di Azure. Per fare in modo che Jenkins acceda ad Azure senza dover immettere le credenziali dell'utente, creare un'[entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) in Azure Active Directory per Jenkins. Un'entità servizio è un'identità separata che Jenkins può usare per autenticare l'accesso alle risorse di Azure. Per creare questa entità servizio, eseguire il comando dell'interfaccia della riga di comando di Azure [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), dalla riga di comando locale o da Azure Cloud Shell, ad esempio: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Assicurarsi di racchiudere tra virgolette il nome dell'entità servizio. Creare inoltre una password complessa basata su [regole e restrizioni per le password di Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Se non si specifica una password, la password viene creata automaticamente dall'interfaccia della riga di comando di Azure. 

Ecco l'output generato dal comando **`create-for-rbac`**: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Se si ha già un'entità servizio, è possibile riutilizzarla.
> Quando si forniscono i valori dell'entità servizio per l'autenticazione, usare i valori delle proprietà `appId`, `password` e `tenant`. 
> Quando si cerca un'entità servizio esistente, usare il valore della proprietà `displayName`.

## <a name="add-service-principal-to-jenkins"></a>Aggiungere l'entità servizio a Jenkins

1. Nella pagina principale di Jenkins selezionare **Credentials** > **System** (Credenziali > Sistema). 

1. Nella pagina **System** (Sistema) in **Domain** (Dominio) selezionare **Global credentials (unrestricted)** (Credenziali globali - senza restrizioni).

1. Dal menu a sinistra scegliere **Add Credentials** (Aggiungi credenziali).

1. Nell'elenco **Kind** (Tipo) selezionare **Azure Service Principal** (Entità servizio di Azure).

1. Fornire le informazioni per l'entità servizio e la sottoscrizione di Azure nelle proprietà descritte dalla tabella in questo passaggio:

   ![Aggiungere le credenziali dell'entità servizio di Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Proprietà | Valore | DESCRIZIONE | 
   |----------|-------|-------------| 
   | **ID sottoscrizione** | <*ID-SottoscrizioneAzure*> | Valore GUID per la sottoscrizione di Azure <p>**Suggerimento**: se non si conosce l'ID sottoscrizione di Azure, eseguire questo comando dell'interfaccia della riga di comando di Azure dalla riga di comando o in Cloud Shell e quindi usare il valore GUID `id`: <p>`az account list` | 
   | **ID client** | <*ID-EntitàServizioAzure*> | Valore GUID `appId` generato in precedenza per l'entità servizio di Azure | 
   | **Segreto client** | <*PasswordSicura*> | Valore `password` o "segreto" specificato per l'entità servizio di Azure | 
   | **ID tenant** | <*ID-TenantAzureActiveDirectory*> | Valore GUID `tenant` per il tenant di Azure Active Directory | 
   | **ID** | <*NomeEntitàServizioAzure*> | Valore `displayName` per l'entità servizio di Azure | 
   |||| 

1. Per verificare il corretto funzionamento dell'entità servizio, scegliere **Verify Service Principal** (Verifica entità servizio). Al termine dell'operazione, scegliere **OK**.

Creare quindi la pipeline Jenkins che compila e distribuisce l'app.

## <a name="create-jenkins-pipeline"></a>Creare una pipeline Jenkins

In Jenkins creare il processo di pipeline per la compilazione e la distribuzione dell'app.

1. Tornare alla home page di Jenkins e selezionare **New Item** (Nuovo elemento). 

   ![Selezionare "Nuovo elemento"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Specificare un nome per il processo di pipeline, ad esempio, "My-Java-Web-App" e selezionare **Pipeline**. Nella parte inferiore scegliere **OK**.  

   ![Selezionare "Pipeline"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Configurare Jenkins con l'entità servizio in modo che Jenkins possa eseguire la distribuzione in Azure senza usare le credenziali dell'utente.

   1. Nella scheda **Generale** selezionare **Prepare an environment for the run** (Preparare un ambiente per l'esecuzione). 

   1. Nella casella **Properties Content** (Contenuto proprietà) visualizzata aggiungere queste variabili di ambiente e i relativi valori. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Selezionare Prepare an environment for the run (Preparare un ambiente per l'esecuzione) e impostare le variabili di ambiente](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Al termine dell'operazione, scegliere **Salva**.

Creare quindi gli script di compilazione e distribuzione per Jenkins.

## <a name="create-build-and-deployment-files"></a>Creare file di compilazione e distribuzione

Creare ora i file usati da Jenkins per la compilazione e la distribuzione dell'app.

1. Nella cartella `src/main/resources/` del fork di GitHub creare il file di configurazione dell'app denominato `web.config`, contenente questo codice XML, ma sostituire `$(JAR_FILE_NAME)` con `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Nella cartella radice del fork di GitHub creare questo script di compilazione e distribuzione denominato `Jenkinsfile`, contenente questo testo ([codice sorgente disponibile in GitHub qui](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Eseguire il commit dei file `web.config` e `Jenkinsfile` nel fork di GitHub ed eseguire il push delle modifiche.

## <a name="point-pipeline-at-script"></a>Puntare la pipeline allo script

Specificare ora lo script di compilazione e distribuzione che Jenkins deve usare.

1. In Jenkins selezionare il processo di pipeline creato in precedenza. 

   ![Selezionare il processo di pipeline per l'app Web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. Nel menu a sinistra selezionare **Configure** (Configura).

1. Nell'elenco **Definition** (Definizione) della scheda **Pipeline** selezionare **Pipeline script from SCM** (Script di pipeline da SCM).

   1. Nella casella **SCM** visualizzata selezionare **Git** come controllo del codice sorgente. 

   1. Nella sezione **Repositories** (Repository), per **Repository URL** (URL del repository) immettere l'URL del fork di GitHub, ad esempio: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Per **Credentials** (Credenziali) selezionare il token di accesso personale di GitHub creato in precedenza.

   1. Nella casella **Script Path** (Percorso script) aggiungere il percorso dello script "Jenkinsfile".

   Al termine, la definizione della pipeline avrà un aspetto simile a questo esempio: 

   ![Puntare la pipeline allo script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Al termine dell'operazione, scegliere **Salva**.

Compilare e distribuire quindi l'app in Servizio app di Azure. 

## <a name="build-and-deploy-to-azure"></a>Eseguire la compilazione e la distribuzione in Azure

1. Con l'interfaccia della riga di comando di Azure, dalla riga di comando o da Azure Cloud Shell, creare un'[app Web di Servizio app di Azure in Linux](/azure/app-service/containers/app-service-linux-intro) dove Jenkins distribuirà l'app Web dopo il completamento della compilazione. Assicurarsi che l'app Web abbia un nome univoco.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Per altre informazioni su questi comandi dell'interfaccia della riga di comando di Azure, vedere queste pagine:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. In Jenkins selezionare il processo di pipeline e quindi **Build Now** (Compila).

   Al termine della compilazione, Jenkins distribuisce l'app, che è ora disponibile in Azure nell'URL di pubblicazione, ad esempio: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Visualizzare l'app distribuita in Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Eseguire il push delle modifiche e ripetere la ridistribuzione

1. Nel Web browser passare a questo percorso nel fork di GitHub dell'app Web:

   `complete/src/main/java/Hello/Application.java`
   
1. Nell'angolo superiore destro in GitHub scegliere **Edit this file** (Modifica questo file).

1. Apportare la modifica seguente al metodo `commandLineRunner()` ed eseguirne il commit nel ramo `master` del repository. Questo commit nel ramo `master` avvia una compilazione in Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Al termine della compilazione, dopo che Jenkins ha eseguito la ridistribuzione in Azure, aggiornare l'app, in cui sarà ora presente l'aggiornamento apportato.

   ![Visualizzare l'app distribuita in Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Risoluzione dei problemi del plug-in Jenkins

Se si rilevano bug con i plug-in Jenkins, segnalare il problema in [Jenkins JIRA](https://issues.jenkins-ci.org/) per il componente specifico.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare macchine virtuali di Azure come agenti di compilazione](/azure/jenkins/jenkins-azure-vm-agents)
