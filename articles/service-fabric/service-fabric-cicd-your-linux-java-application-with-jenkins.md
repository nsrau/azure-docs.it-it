---
title: Compilazione continua e integrazione per applicazioni Java Linux di Azure Service Fabric tramite Jenkins | Microsoft Docs
description: Compilazione continua e integrazione per applicazioni Java Linux tramite Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Creare e distribuire applicazioni Java Linux usando Jenkins
Jenkins è uno strumento diffuso per l'integrazione e la distribuzione continue. Questo documento illustra in modo dettagliato la creazione e la distribuzione di un'applicazione Service Fabric con Jenkins.

## <a name="general-prerequisites"></a>Prerequisiti generali
1. È necessario che Git sia installato in locale. È possibile installare la versione appropriata di git da [qui](https://git-scm.com/downloads) in base al sistema operativo in uso. Se non si conosce Git, è possibile seguire la procedura indicata nella relativa [documentazione](https://git-scm.com/docs) per acquisire maggiore familiarità.
2. È necessario avere a disposizione il plug-in Jenkins per Service Fabric. È possibile scaricare il plug-in Jenkins per Service Fabric da [qui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configurazione di Jenkins all'interno di un cluster di Service Fabric

### <a name="prerequisites"></a>Prerequisiti
1. Un cluster Linux Service Fabric pronto. Docker è già installato nei cluster di Service Fabric creati dal portale di Azure. Se si esegue il cluster in locale, controllare se Docker è installato o meno usando il comando ``docker info``. Se non è installato, installarlo usando i comandi seguenti:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Applicazione contenitore di Service Fabric distribuita nel cluster seguendo questa procedura:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Il contenitore Jenkins viene installato nel cluster e può essere monitorato tramite Service Fabric Explorer.

### <a name="steps"></a>Passi
1. Passare all'URL ``http://PublicIPorFQDN:8081`` dal browser. Fornisce il percorso della password amministratore iniziale necessaria per eseguire l'accesso. Dopo aver eseguito l'accesso con l'account amministratore iniziale, è possibile continuare a usare Jenkins come utente amministratore oppure creare e modificare l'utente.

  > [!NOTE]
  > È necessario verificare che la porta 8081 sia specificata come porta di endpoint dell'applicazione durante la creazione del cluster.
  >

2. Ottenere l'ID dell'istanza del contenitore con ``docker ps -a``.
3. Eseguire l'accesso SSH al contenitore e incollare il percorso visualizzato nel portale di Jenkins. Ad esempio, se nel portale viene visualizzato il percorso `PATH_TO_INITIAL_ADMIN_PASSWORD`, è possibile eseguire:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configurare GitHub per il funzionamento con Jenkins seguendo la procedura illustrata in [questo articolo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita il repository.
    * Eseguire i comandi specificati nell'articolo indicato in precedenza nella shell Docker di Jenkins, non nell'host.
    * Per accedere alla shell di Jenkins dall'host, eseguire questo comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configurazione di Jenkins all'esterno di un cluster di Service Fabric

### <a name="prerequisites"></a>Prerequisiti
È necessario che Docker sia installato. Per installare Docker dal terminale, è possibile usare i comandi seguenti:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

A questo punto, quando si esegue ``docker info`` nel terminale, l'output indica che il servizio Docker è in esecuzione.

### <a name="steps"></a>Passi
  1. Effettuare il pull dell'immagine del contenitore di Jenkins per Service Fabric: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Eseguire l'immagine del contenitore: ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Ottenere l'ID istanza dell'immagine del contenitore. È possibile elencare tutti i contenitori Docker con il comando ``docker ps –a``
  4. Accedere al portale di Jenkins seguendo questa procedura:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se l'ID contenitore è 2d24a73b5964, usare 2d24.
    * Questa password è necessaria per l'accesso al dashboard di Jenkins dal portale ovvero ``http://<HOST-IP>:8080``.
    * Dopo il primo accesso, è possibile creare il proprio account utente e usarlo per le operazioni successive oppure è possibile continuare a usare l'account amministratore. Dopo aver creato un utente, è necessario continuare a usarlo.
  5. Configurare GitHub per il funzionamento con Jenkins seguendo la procedura illustrata in [questo articolo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita (dovrebbe ospitare) il repository.
      * Eseguire i comandi specificati nell'articolo indicato in precedenza nella shell Docker di Jenkins, non nell'host.
      * Per accedere alla shell di Jenkins dall'host, eseguire questi comandi:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Assicurarsi che il cluster o il computer in cui è ospitata l'immagine del contenitore di Jenkins abbia un indirizzo IP esposto al pubblico, in modo che le notifiche da GitHub vengono ricevute dall'istanza di Jenkins.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Installare il plug-in Jenkins per Service Fabric dal portale

1. Passare a ``http://PublicIPorFQDN:8081``.
2. Dal dashboard di Jenkins selezionare **Manage Jenkins** (Gestione Jenkins) -> **Manage Plugins** (Gestione plug-in) -> **Advanced** (Avanzate).
In questa schermata è possibile caricare un plug-in. Selezionare l'opzione **Choose file** (Scegli file) e quindi selezionare il file serviceFabric.hpi scaricato nella sezione dei prerequisiti. Dopo aver selezionato l'opzione per il caricamento, Jenkins installa automaticamente il plug-in. Se richiesto, consentire il riavvio.

## <a name="creating-and-configuring-a-jenkins-job"></a>Creazione e configurazione di un processo Jenkins

1. Creare un **nuovo elemento** dal dashboard.
2. Immettere un nuovo nome di elemento, ad esempio **MyJob**, selezionare un progetto libero e fare clic su OK.
3. Passare quindi alla pagina del processo e fare clic su **Configura** -
  * Nella sezione generale, in **GitHub project** (Progetto GitHub) specificare l'URL del progetto GitHub che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso CI/CD di Jenkins, ad esempio: ``https://github.com/sayantancs/SFJenkins``.
  * Nella sezione **Source Code Management** (Gestione del codice sorgente) selezionare **Git**. Specificare l'URL del repository che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso CI/CD di Jenkins, ad esempio: ``https://github.com/sayantancs/SFJenkins.git``. Qui è anche possibile specificare il ramo da compilare, ad esempio: ***/master**.
4. Per configurare *GitHub*, che ospita il repository, in modo che possa comunicare con Jenkins, seguire questa procedura:
  1. Passare alla pagina del repository GitHub. Passare a **Settings** (Impostazioni)  -> **Integrations and Services** (Integrazioni e servizi).
  2. Selezionare **Add Service** (Aggiungi servizio), digitare Jenkins e selezionare il **plug-in GitHub per Jenkins**.
  3. Immettere l'URL webhook di Jenkins (per impostazione predefinita, ``http://<PublicIPorFQDN>:8081/github-webhook/``). Fare clic sull'opzione per aggiungere o aggiornare il servizio.
  4. Verrà inviato un evento di test all'istanza di Jenkins. Accanto al webhook in GitHub verrà visualizzato un segno di spunta verde e il progetto verrà compilato.
  5. Nella sezione **Build Triggers** (Trigger di compilazione) selezionare l'opzione di compilazione da usare. In questo caso, si intende attivare una compilazione ogni volta che si verifica un push nel repository, quindi l'opzione corrispondente è **GitHub hook trigger for GITScm polling** (Trigger hook GitHub per il polling GITScm). In precedenza questa opzione era denominata "Build when a change is pushed to GitHub" (Compila in seguito al push di una modifica in GitHub).
  6. Nella sezione **Build** (Compilazione) selezionare l'opzione **Invoke Gradle Script** (Richiama script Gradle) dall'elenco a discesa **Add build step** (Aggiungi passaggio di compilazione). Nel widget visualizzato specificare il percorso per **Root build script** (Script di compilazione radice), per l'applicazione. Viene estratto il file build.gradle dal percorso specificato che opera corrispondentemente. Se si crea un progetto denominato ``MyActor`` usando il plug-in Eclipse o il generatore Yeoman, lo script di compilazione radice deve contenere ``${WORKSPACE}/MyActor``. Ad esempio, questa sezione avrà molto probabilmente un aspetto simile al seguente:

    ![Azione di compilazione di Jenkins per Service Fabric][build-step]
  7. Nell'elenco a discesa **Post-Build Actions** (Azioni post-compilazione) selezionare **Deploy Service Fabric Project** (Distribuisci progetto di Service Fabric). In questa finestra è necessario specificare i dettagli del cluster in cui verrà distribuita l'applicazione di Service Fabric compilata con Jenkins e i dettagli aggiuntivi per l'applicazione usati per distribuirla. È possibile usare lo screenshot seguente come riferimento:

    ![Azione di compilazione di Jenkins per Service Fabric][post-build-step]

 > [!NOTE]
 > In questo caso il cluster potrebbe essere lo stesso che ospita l'applicazione contenitore Jenkins, nel caso si usi Service Fabric per distribuire l'immagine del contenitore Jenkins.
 >

### <a name="end-to-end-scenario"></a>Scenario end-to-end
A questo punto, GitHub e Jenkins sono configurati. Ora è possibile apportare alcune modifiche di esempio al progetto ``MyActor`` nel repository di esempio https://github.com/sayantancs/SFJenkins ed effettuare il push delle modifiche nel ramo ``master`` remoto o in qualsiasi ramo configurato per l'uso. Verrà così attivato il processo Jenkins ``MyJob`` configurato. Le operazioni svolte dal processo sono fondamentalmente il recupero delle modifiche da GitHub, la compilazione delle modifiche e la distribuzione dell'applicazione nell'endpoint del cluster specificato nelle azioni di post-compilazione.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

