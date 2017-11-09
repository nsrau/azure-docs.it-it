---
title: Compilazione e integrazione continue per un'applicazione Java Linux di Azure Service Fabric tramite Jenkins | Microsoft Docs
description: Compilazione continua e integrazione per un'applicazione Java Linux tramite Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.openlocfilehash: d9870fafab3df3ab0ec72305e76a4d3547cc5b2c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Usare Jenkins per compilare e distribuire l'applicazione Java Linux
Jenkins è uno strumento diffuso per l'integrazione e la distribuzione continue delle app. Ecco come compilare e distribuire l'applicazione di Azure Service Fabric usando Jenkins.

## <a name="general-prerequisites"></a>Prerequisiti generali
- È necessario che Git sia installato in locale. È possibile installare la versione di Git appropriata dalla [pagina di download di Git](https://git-scm.com/downloads), in base al sistema operativo specifico. Se non si ha familiarità con Git, per altre informazioni, vedere la [documentazione di Git](https://git-scm.com/docs).
- È necessario avere a disposizione il plug-in Jenkins per Service Fabric. È possibile scaricarlo dalla pagina di [download di Service Fabric](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurare Jenkins all'interno di un cluster di Service Fabric

È possibile configurare Jenkins all'interno o all'esterno di un cluster di Service Fabric. Le sezioni seguenti mostrano come configurare questa funzionalità in un cluster utilizzando un account di archiviazione Azure per salvare lo stato dell’istanza del contenitore.

### <a name="prerequisites"></a>Prerequisiti
1. Un cluster Linux Service Fabric pronto. Docker è già installato in un cluster di Service Fabric creato dal portale di Azure. Se si esegue il cluster localmente, verificare se Docker è installato usando il comando ``docker info``. Se non è installato, installarlo usando i comandi seguenti:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Verificare che la porta 8081 sia specificata come endpoint personalizzato nel cluster.
   >
2. Clone dell'applicazione usando la procedura seguente:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
cd service-fabric-java-getting-started/Services/JenkinsDocker/
```

3. Mantenere lo stato del contenitore di Jenkins in una condivisione file:
  * Creare un account di archiviazione di Azure nella **stessa regione** del cluster denominato ``sfjenkinsstorage1``.
  * Creare una **condivisione file** con l'account di archiviazione denominato``sfjenkins``.
  * Fare clic su **Connetti** per la condivisione file e annotare i valori visualizzati in **Connessione da Linux**. Il valore è simile a quello riportato di seguito:
```sh
sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
```

> [!NOTE]
> Per montare le condivisioni cifs, è necessario che il pacchetto cifs-utils sia installato nei nodi del cluster.         
>

4. Aggiornare i valori segnaposto nello script ```setupentrypoint.sh``` con i corrispondenti dettagli di archiviazione di Azure riportati nel passaggio 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Sostituire ``[REMOTE_FILE_SHARE_LOCATION]`` con il valore ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` dall'output di connessione al passaggio 3 sopra.
  * Sostituire ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` con il valore ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` dal passaggio 3 sopra.

5. Connettersi al cluster e installare l'applicazione contenitore.
```sh
sfctl cluster select --endpoint http://PublicIPorFQDN:19080   # cluster connect command
bash Scripts/install.sh
```
Un contenitore Jenkins viene installato nel cluster e può essere monitorato tramite Service Fabric Explorer.

   > [!NOTE]
   > Lo scaricamento dell'immagine di Jenkins nel cluster potrebbe richiedere alcuni minuti.
   >

### <a name="steps"></a>Passi
1. Nel browser passare a ``http://PublicIPorFQDN:8081``. Fornisce il percorso della password amministratore iniziale necessaria per eseguire l'accesso. 
2. Esaminare Service Fabric Explorer per determinare su quale nodo è in esecuzione il contenitore di Jenkins. Eseguire l'accesso SSH (Secure Shell) a questo nodo.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Ottenere l'ID dell'istanza del contenitore con ``docker ps -a``.
4. Eseguire l'accesso SSH (Secure Shell) al contenitore e incollare il percorso visualizzato nel portale di Jenkins. Ad esempio, se nel portale viene visualizzato il percorso `PATH_TO_INITIAL_ADMIN_PASSWORD`, eseguire il comando seguente:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. Nella pagina Jenkins Getting Started (Introduzione a Jenkins) selezionare l'opzione Select plugins to install (Seleziona plug-in da installare), selezionare la casella di controllo **None** (Nessuno) e quindi fare clic su Install (Installa).
6. Creare un utente o selezionare questa opzione per continuare come amministratore.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurare Jenkins all'esterno di un cluster di Service Fabric

È possibile configurare Jenkins all'interno o all'esterno di un cluster di Service Fabric. Le sezioni seguenti illustrano come configurarlo all'esterno di un cluster.

### <a name="prerequisites"></a>Prerequisiti
È necessario che Docker sia installato. Per installare Docker dal terminale, è possibile usare i comandi seguenti:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

A questo punto, quando si esegue ``docker info`` nel terminale, l'output indica che il servizio Docker è in esecuzione.

### <a name="steps"></a>Passi
  1. Effettuare il pull dell'immagine del contenitore di Jenkins per Service Fabric: ``docker pull raunakpandya/jenkins:v1``
  2. Eseguire l'immagine del contenitore: ``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Ottenere l'ID istanza dell'immagine del contenitore. È possibile elencare tutti i contenitori Docker con il comando ``docker ps –a``
  4. Accedere al portale di Jenkins seguendo questa procedura:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se l'ID contenitore è 2d24a73b5964, usare 2d24.
    * Questa password è necessaria per l'accesso al dashboard di Jenkins dal portale ovvero ``http://<HOST-IP>:8080``.
    * Dopo il primo accesso, è possibile creare il proprio account utente e usarlo per le operazioni successive oppure è possibile continuare a usare l'account amministratore. Dopo aver creato un utente, è necessario continuare a usarlo.
  5. Configurare GitHub per l'interazione con Jenkins seguendo la procedura illustrata in [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generazione di una nuova chiave SSH e aggiunta della chiave all'agente SSH).
        * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita il repository.
        * Eseguire i comandi specificati nell'articolo indicato in precedenza nella shell Docker di Jenkins, non nell'host.
      * Per accedere alla shell di Jenkins dall'host, eseguire questi comandi:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Assicurarsi che il cluster o il computer in cui è ospitata l'immagine del contenitore Jenkins abbia un indirizzo IP pubblico. Ciò consente all'istanza di Jenkins di ricevere notifiche da GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Installare il plug-in Jenkins per Service Fabric dal portale

1. Passare a ``http://PublicIPorFQDN:8081``.
2. Dal dashboard di Jenkins selezionare **Manage Jenkins** (Gestione Jenkins) > **Manage Plugins** (Gestione plug-in) > **Advanced** (Avanzate).
In questa schermata è possibile caricare un plug-in. Selezionare l'opzione **Choose file** (Scegli file) e quindi selezionare il file **serviceFabric.hpi** scaricato nella sezione dei prerequisiti oppure scaricabile [qui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi). Dopo aver selezionato l'opzione **Upload** (Carica), Jenkins installa automaticamente il plug-in. Se richiesto, consentire il riavvio.

## <a name="create-and-configure-a-jenkins-job"></a>Creare e configurare un processo Jenkins

1. Creare un **nuovo elemento** dal dashboard.
2. Immettere un nome per l'elemento, ad esempio **MyJob**. Selezionare **free-style project** (progetto libero) e fare clic su **OK**.
3. Passare quindi alla pagina del processo e fare clic su **Configure** (Configura).

   a. Nella sezione generale selezionare la casella di controllo associata a **GitHub project** (Progetto GitHub) e specificare l'URL del progetto GitHub. Questo URL ospita l'applicazione Java di Service Fabric da integrare con il flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio ``https://github.com/sayantancs/SFJenkins``.

   b. Nella sezione **Source Code Management** (Gestione del codice sorgente) selezionare **Git**. Specificare l'URL del repository che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio ``https://github.com/sayantancs/SFJenkins.git``. Qui è anche possibile specificare il ramo da compilare, ad esempio **/master**.
4. Configurare *GitHub*, che ospita il repository, in modo che possa comunicare con Jenkins. Seguire questa procedura:

   a. Passare alla pagina del repository GitHub. Passare a **Settings** (Impostazioni)  > **Integrations and Services** (Integrazioni e servizi).

   b. Selezionare **Add Service** (Aggiungi servizio), digitare **Jenkins** e selezionare il **plug-in GitHub per Jenkins**.

   c. Immettere l'URL webhook di Jenkins (per impostazione predefinita, ``http://<PublicIPorFQDN>:8081/github-webhook/``). Fare clic su **add/update service** (aggiungi/aggiorna il servizio).

   d. Verrà inviato un evento di test all'istanza di Jenkins. Accanto al webhook in GitHub verrà visualizzato un segno di spunta verde e il progetto verrà compilato.

   e. Nella sezione **Build Triggers** (Trigger compilazione) selezionare l'opzione di compilazione da usare. Per questo esempio è necessario attivare una compilazione a ogni push nel repository. Selezionare quindi l'opzione **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm). Questa opzione era definita in precedenza **Build when a change is pushed to GitHub** (Compila in caso di push di modifiche in GitHub).

   f. Nella sezione **Build** (Compilazione) selezionare l'opzione **Add build step** (Richiama script Gradle) dall'elenco a discesa **Invoke Gradle Script** (Aggiungi passaggio di compilazione). Nel widget visualizzato aprire il menu Advanced (Avanzato) e specificare il percorso per **Root build script** (Script di compilazione radice) per l'applicazione. Viene estratto il file build.gradle dal percorso specificato che opera in modo corrispondente. Se si crea un progetto denominato ``MyActor`` usando il plug-in Eclipse o il generatore Yeoman, lo script di compilazione radice deve contenere ``${WORKSPACE}/MyActor``. Per un esempio, vedere la schermata seguente:

    ![Azione di compilazione di Jenkins per Service Fabric][build-step]

   g. Nell'elenco a discesa **Post-Build Actions** (Azioni post-compilazione) selezionare **Deploy Service Fabric Project** (Distribuisci progetto di Service Fabric). In questa finestra è necessario specificare i dettagli del cluster in cui verrà distribuita l'applicazione di Service Fabric compilata con Jenkins. È anche possibile specificare dettagli aggiuntivi per l'applicazione usati per distribuirla. Per un esempio, vedere la schermata seguente:

    ![Azione di compilazione di Jenkins per Service Fabric][post-build-step]

   > [!NOTE]
   > In questo caso il cluster potrebbe essere lo stesso che ospita l'applicazione contenitore Jenkins, nel caso si usi Service Fabric per distribuire l'immagine del contenitore Jenkins.
   >

## <a name="next-steps"></a>Passaggi successivi
GitHub e Jenkins sono ora configurati. Prendere in considerazione alcune modifiche di esempio nel progetto ``MyActor`` per l'esempio del repository, https://github.com/sayantancs/SFJenkins. Eseguire il push delle modifiche a un ramo ``master`` remoto o a qualsiasi ramo configurato per l'uso. Viene così attivato il processo Jenkins ``MyJob`` configurato. Vengono eseguite le operazioni di recupero delle modifiche da GitHub, compilazione delle modifiche e distribuzione dell'applicazione nell'endpoint del cluster specificato nelle azioni di post-compilazione.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png
