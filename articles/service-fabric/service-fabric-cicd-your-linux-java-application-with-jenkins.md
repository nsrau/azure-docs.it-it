---
title: Creazione e distribuzione continue per applicazioni Java Linux usando Jenkins | Microsoft Docs
description: Creazione e distribuzione continue per applicazioni Java Linux usando Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Creare e distribuire applicazioni Java Linux usando Jenkins
Jenkins è uno strumento diffuso per l'integrazione e la distribuzione continue. Questo documento illustra in modo dettagliato la creazione e la distribuzione di un'applicazione Service Fabric con Jenkins.

## <a name="general-prerequisites"></a>Prerequisiti generali
1. È necessario disporre di git installato in locale. È possibile installare la versione appropriata di git da [qui](https://git-scm.com/downloads) in base al sistema operativo in uso. Se non si conosce git, è possibile seguire i passaggi indicati nella [documentazione](https://git-scm.com/docs) per acquisire maggiore familiarità.
2. È necessario avere a disposizione il plug-in Jenkins per Service Fabric. È possibile scaricare il plug-in Jenkins per Service Fabric da [qui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configurazione di Jenkins all'interno di un cluster di Service Fabric

### <a name="prerequisites"></a>Prerequisiti
1. Un cluster Linux Service Fabric pronto. Docker è già installato nei cluster di Service Fabric creati dal portale di Azure. Se si esegue il cluster locale, controllare se Docker è installato con il comando ``docker info``. Se non è installato, installarlo con i comandi seguenti:
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. Applicazione contenitore di Service Fabric distribuita nel cluster ``http://PublicIPorFQDN:19080``. È possibile eseguire i passaggi seguenti:
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  Il contenitore Jenkins verrà installato nel cluster connesso. È possibile monitorare l'applicazione contenitore usando Service Fabric Explorer.

### <a name="steps"></a>Passi
1. Passare all'URL ``http://PublicIPorFQDN:8081`` dal browser. Verrà fornito il percorso della password di amministratore iniziale necessaria per eseguire l'accesso. È possibile continuare a usare Jenkins come utente amministratore o è possibile creare e modificare l'utente, dopo aver eseguito l'accesso con l'account amministratore iniziale.
> [!NOTE]
> È necessario verificare che la porta 8081 sia specificata come porta di endpoint dell'applicazione durante la creazione del cluster.
>

2. Ottenere l'ID dell'istanza del contenitore con ``docker ps -a``.
3. Eseguire l'accesso SSH al contenitore e incollare il percorso visualizzato nel portale di Jenkins. Ad esempio, se nel portale viene visualizzato il percorso `PATH_TO_INITIAL_ADMIN_PASSWORD`, è possibile eseguire:  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configurare GitHub per l'interazione con Jenkins usando [questo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita (dovrebbe ospitare) il repository.
    * Eseguire i comandi indicati nel collegamento precedente nella shell di Docker di Jenkins (e non nell'host).
    * Per accedere alla shell di Jenkins dall'host, è necessario eseguire il comando seguente:
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configurazione di Jenkins all'esterno di un cluster di Service Fabric

### <a name="prerequisites"></a>Prerequisiti
1. È necessario disporre di Docker installato. In caso contrario, digitare i comandi seguenti nel terminale per installare Docker.
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
A questo punto, quando si esegue ``docker info`` nel terminale, l'output indica che il servizio Docker è in esecuzione.

### <a name="steps"></a>Passi
  1. Eseguire il pull del contenitore di Jenkins di Service Fabric: ``docker pull IMAGE_NAME ``
  2. Eseguire l'immagine del contenitore: ``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. Ottenere l'ID del contenitore Docker con l'immagine di Jenkins (appena installata). È possibile elencare tutti i contenitori di Docker con il comando ``docker ps –a``
  4. Ottenere la password di amministratore per l'account Jenkins. A tale scopo, è possibile eseguire il comando seguente:
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se l'ID del contenitore è 2d24a73b5964, è necessario inserire solo 2d24
    * Questa password sarà necessaria per l'accesso al dashboard di Jenkins dal portale ovvero ``http://<HOST-IP>:8080``.
    * Dopo aver eseguito l'accesso per la prima volta, è possibile creare il proprio account utente e usarlo per le operazioni successive oppure è possibile continuare a usare l'account amministratore. Dopo aver creato un nuovo utente, è necessario continuare con tale utente.
  5. Configurare GitHub per l'interazione con Jenkins usando [questo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita (dovrebbe ospitare) il repository.
      * Eseguire i comandi indicati nel collegamento precedente nella shell di Docker di Jenkins (e non nell'host).
      * Per accedere alla shell di Jenkins dall'host, è necessario eseguire il comando seguente:
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> Assicurarsi che il cluster o il computer in cui è ospitata l'immagine del contenitore di Jenkins abbia un indirizzo IP esposto al pubblico, in modo che le notifiche da GitHub vengono ricevute dall'istanza di Jenkins.
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Installare il plug-in Jenkins per Service Fabric dal portale

1. Passare a ``http://PublicIPorFQDN:8081``.
2. Dal dashboard di Jenkins selezionare ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``.
In questa schermata è possibile caricare un plug-in. Selezionare l'opzione ``Choose file`` e quindi selezionare il file serviceFabric.hpi scaricato nella sezione dei prerequisiti. Dopo aver selezionato l'opzione per il caricamento, Jenkins installerà automaticamente il plug-in. Nel caso venga richiesto di riavviare il sistema, acconsentire all'operazione.

## <a name="creating-and-configuring-a-jenkins-job"></a>Creazione e configurazione di un processo Jenkins

1. Creare un ``new item`` dal dashboard.
2. Immettere un nuovo nome di elemento, ad esempio ``MyJob``, selezionare un progetto Freestyle e fare clic su OK.
3. Passare quindi alla pagina del processo e fare clic su ``Configure`` -.
  * Nella sezione generale, in ``Github project``, specificare l'URL del progetto GitHub che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso CI/CD di Jenkins (ad esempio: ``https://github.com/sayantancs/SFJenkins``).
  * Nella sezione ``Source Code Management`` selezionare ``Git``. Specificare l'URL del repository che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso CI/CD di Jenkins (ad esempio: ``https://github.com/sayantancs/SFJenkins.git``). Qui è anche possibile specificare il ramo da compilare, ad esempio: ``*/master``.
4. Configurare *Github* (che ospita il repository) in modo che sia in grado di comunicare con Jenkins, usando questa procedura:
  - Passare alla pagina del repository GitHub. Passare a ``Settings`` -> ``Integrations and Services``.
  - Selezionare ``Add Service``, digitare Jenkins, selezionare ``Jenkins-Github plugin``.
  - Immettere l'URL webhook di Jenkins (per impostazione predefinita, ``http://<PublicIPorFQDN>:8081/github-webhook/``). Fare clic sull'opzione per aggiungere o aggiornare il servizio.
  - Verrà inviato un evento di test all'istanza di Jenkins. Accanto al webhook in GitHub verrà visualizzato un segno di spunta verde e il progetto verrà compilato.
  - Nella sezione ``Build Triggers`` selezionare l'opzione di compilazione desiderata. In questo caso, si intende attivare una compilazione ogni volta che si verifica un push nel repository, quindi l'opzione corrispondente è ``GitHub hook trigger for GITScm polling``. Questa opzione era in precedenza 'Build when a change is pushed to GitHub' (Compila in seguito a push di una modifica in GitHub).
  - In ``Build section`` selezionare l'opzione ``Invoke Gradle Script`` nell'elenco a discesa ``Add build step``. Nel widget visualizzato specificare il percorso per ``Root build script``, per l'applicazione. Viene estratto il file build.gradle dal percorso specificato che opera corrispondentemente. Si noti che se viene creato un progetto denominato ``MyActor``(con il plug-in Eclipse o il generatore Yeoman), quindi lo script di compilazione radice deve contenere ``${WORKSPACE}/MyActor``. Ad esempio, questa sezione avrà molto probabilmente un aspetto simile al seguente:

    ![Azione di compilazione di Jenkins per Service Fabric][build-step]
  - Nell'elenco a discesa ``Post-Build Actions`` selezionare ``Deploy Service Fabric Project``. In questa finestra è necessario specificare i dettagli del cluster in cui verrà distribuita l'applicazione di Service Fabric compilata con Jenkins e i dettagli aggiuntivi per l'applicazione usati per distribuirla. È possibile usare lo screenshot seguente come riferimento:

    ![Azione di compilazione di Jenkins per Service Fabric][post-build-step]

 >[!Note]
 > In questo caso il cluster potrebbe essere lo stesso che ospita l'applicazione contenitore Jenkins, nel caso si usi Service Fabric per distribuire l'immagine del contenitore Jenkins.
 >

### <a name="end-to-end-scenario"></a>Scenario end-to-end
A questo punto, GitHub e Jenkins sono configurati ed è ora possibile apportare alcune modifiche di esempio nel progetto ``MyActor`` nel repository, ad esempio https://github.com/sayantancs/SFJenkins ed eseguire il push delle modifiche nel ramo ``master`` remoto (o qualsiasi ramo configurato per l'utilizzo). Verrà così attivato il processo Jenkins ``MyJob`` configurato. Le operazioni svolte dal processo sono fondamentalmente il recupero delle modifiche da GitHub, la compilazione delle modifiche e la distribuzione dell'applicazione nell'endpoint del cluster specificato nelle azioni di post-compilazione.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

