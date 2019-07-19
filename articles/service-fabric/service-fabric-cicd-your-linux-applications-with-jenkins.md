---
title: Compilazione e integrazione continue per applicazioni Linux di Azure Service Fabric tramite Jenkins | Microsoft Docs
description: Compilazione e integrazione continue per l'applicazione Linux di Azure Service Fabric tramite Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: jeconnoc
ms.openlocfilehash: b757a0a5f3ce968b396fa89d5b32c18257d620c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875092"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Usare Jenkins per compilare e distribuire le applicazioni Linux
Jenkins è uno strumento diffuso per l'integrazione e la distribuzione continue delle app. Ecco come compilare e distribuire l'applicazione di Azure Service Fabric usando Jenkins.

## <a name="topic-overview"></a>Panoramica dell'argomento
Questo articolo illustra le diverse modalità possibili di configurazione dell'ambiente Jenkins, oltre ai vari modi per distribuire l'applicazione in un cluster di Service Fabric al termine della compilazione. Seguire questi passaggi generali per configurare correttamente Jenkins, eseguire il pull delle modifiche da GitHub, compilare l'applicazione e distribuirla al cluster:

1. Assicurarsi di avere installato i [prerequisiti](#prerequisites).
1. Seguire quindi i passaggi descritti in una di queste sezioni per configurare Jenkins:
   * [Configurare Jenkins all'interno di un cluster di Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Configurare Jenkins all'esterno di un cluster di Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster) o
   * [Installare il plug-in Service Fabric in un ambiente Jenkins esistente](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Dopo avere configurato Jenkins, seguire i passaggi descritti in [Creare e configurare un processo Jenkins](#create-and-configure-a-jenkins-job) per impostare GitHub in modo da attivare Jenkins quando vengono apportate modifiche all'applicazione e configurare la pipeline di processo Jenkins tramite l'istruzione di compilazione per eseguire il pull delle modifiche da GitHub e compilare l'applicazione. 
1. Configurare infine l'istruzione di post-compilazione del processo Jenkins per distribuire l'applicazione al cluster di Service Fabric. Esistono due modi per configurare Jenkins per la distribuzione dell'applicazione a un cluster:    
   * Per gli ambienti di sviluppo e test, andare a [Configurare la distribuzione tramite l'endpoint di gestione del cluster](#configure-deployment-using-cluster-management-endpoint). Si tratta del metodo di distribuzione più semplice da configurare.
   * Per gli ambienti di produzione, andare a [Configurare la distribuzione tramite le credenziali di Azure](#configure-deployment-using-azure-credentials). Questo è il metodo consigliato da Microsoft per gli ambienti di produzione perché con le credenziali di Azure è possibile limitare l'accesso di un processo Jenkins alle risorse di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Verificare che Git sia installato in locale. È possibile installare la versione di Git appropriata dalla [pagina di download di Git](https://git-scm.com/downloads) in base al sistema operativo specifico. Se non si ha familiarità con Git, per altre informazioni vedere la [documentazione di Git](https://git-scm.com/docs).
- Questo articolo usa l'*esempio di introduzione a Service Fabric* in GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) per l'applicazione da compilare e distribuire. È possibile creare una copia tramite fork del repository per proseguire la procedura o, con alcune modifiche alle istruzioni, usare un progetto GitHub personalizzato.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installare il plug-in Service Fabric in un ambiente Jenkins esistente
Se si aggiunge il plug-in Service Fabric in un ambiente Jenkins esistente, è necessario disporre di quanto segue:

- [Interfaccia della riga di comando di Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Assicurarsi di installare l'interfaccia della riga di comando a livello di sistema anziché a livello di utente, in modo che Jenkins possa eseguire i comandi dell'interfaccia della riga di comando. 
   >

- Per distribuire applicazioni Java, installare [Gradle e Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Per distribuire applicazioni .NET Core 2.0, installare [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Dopo avere installato i prerequisiti richiesti per l'ambiente, è possibile cercare il plug-in Azure Service Fabric nel Marketplace per Jenkins e installarlo.

Dopo avere installato il plug-in, passare direttamente a [Creare e configurare un processo Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurare Jenkins all'interno di un cluster di Service Fabric

È possibile configurare Jenkins all'interno o all'esterno di un cluster di Service Fabric. Le sezioni seguenti mostrano come configurare questa funzionalità in un cluster utilizzando un account di archiviazione Azure per salvare lo stato dell’istanza del contenitore.

### <a name="prerequisites"></a>Prerequisiti
- Disporre di un cluster Linux Service Fabric con Docker installato. I cluster di Service Fabric in esecuzione in Azure hanno già Docker installato. Se si esegue il cluster in locale (ambiente di sviluppo OneBox), controllare se Docker è installato nel computer in uso con il comando `docker info`. Se non è installato, installarlo usando i comandi seguenti:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Verificare che la porta 8081 sia specificata come endpoint personalizzato nel cluster. Se si usa un cluster locale, assicurarsi che la porta 8081 sia aperta nel computer host e che disponga di un indirizzo IP pubblico.
   >

### <a name="steps"></a>Passaggi
1. Clonare l'applicazione usando i comandi seguenti:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Mantenere lo stato del contenitore di Jenkins in una condivisione file:
   1. Creare un account di archiviazione di Azure nella **stessa regione** del cluster denominato `sfjenkinsstorage1`.
   1. Creare una **condivisione file** con l'account di archiviazione denominato`sfjenkins`.
   1. Fare clic su **Connetti** per la condivisione file e annotare i valori visualizzati in **Connessione da Linux**. Il valore è simile a quello riportato di seguito:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Per montare le condivisioni cifs, è necessario che il pacchetto cifs-utils sia installato nei nodi del cluster.      
   >

1. Aggiornare i valori segnaposto nello script `setupentrypoint.sh` con i corrispondenti dettagli di archiviazione di Azure riportati nel passaggio 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Sostituire `[REMOTE_FILE_SHARE_LOCATION]` con il valore `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` dall'output di connessione al passaggio 2 sopra.
   * Sostituire `[FILE_SHARE_CONNECT_OPTIONS_STRING]` con il valore `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` del passaggio 2 sopra.

1. **Solo cluster protetto:** 
   
   Per configurare da Jenkins la distribuzione di applicazioni in un cluster protetto, il certificato del cluster deve essere accessibile all'interno del contenitore di Jenkins. Nel file *ApplicationManifest.xml*, nel tag **ContainerHostPolicies**, aggiungere questo riferimento al certificato e aggiornare il valore di identificazione personale con quello del certificato del cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Aggiungere inoltre le righe seguenti nel tag (radice) **ApplicationManifest** nel file *ApplicationManifest.xml* e aggiornare il valore di identificazione personale con quello del certificato del cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Connettersi al cluster e installare l'applicazione contenitore.

   **Cluster protetto**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Il comando precedente accetta il certificato nel formato PEM. Se il certificato è nel formato PFX, è possibile usare il comando seguente per convertirlo. Se il file PFX non è protetto da password, specificare il parametro **passin** come `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Cluster non protetto**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Un contenitore Jenkins viene installato nel cluster e può essere monitorato tramite Service Fabric Explorer.

   > [!NOTE]
   > Lo scaricamento dell'immagine di Jenkins nel cluster potrebbe richiedere alcuni minuti.
   >

1. Nel browser passare a `http://PublicIPorFQDN:8081`. Fornisce il percorso della password amministratore iniziale necessaria per eseguire l'accesso. 
1. Esaminare Service Fabric Explorer per determinare su quale nodo è in esecuzione il contenitore di Jenkins. Eseguire l'accesso SSH (Secure Shell) a questo nodo.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Ottenere l'ID dell'istanza del contenitore con `docker ps -a`.
1. Eseguire l'accesso SSH (Secure Shell) al contenitore e incollare il percorso visualizzato nel portale di Jenkins. Ad esempio, se nel portale viene visualizzato il percorso `PATH_TO_INITIAL_ADMIN_PASSWORD`, eseguire i comandi seguenti:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Nella pagina Jenkins Getting Started (Introduzione a Jenkins) selezionare l'opzione Select plugins to install (Seleziona plug-in da installare), selezionare la casella di controllo **None** (Nessuno) e quindi fare clic su Install (Installa).
1. Creare un utente o selezionare questa opzione per continuare come amministratore.

Dopo avere configurato Jenkins, passare direttamente a [Creare e configurare un processo Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurare Jenkins all'esterno di un cluster di Service Fabric

È possibile configurare Jenkins all'interno o all'esterno di un cluster di Service Fabric. Le sezioni seguenti illustrano come configurarlo all'esterno di un cluster.

### <a name="prerequisites"></a>Prerequisiti
- Assicurarsi che nel computer sia installato Docker. Per installare Docker dal terminale, è possibile usare i comandi seguenti:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Quando si esegue `docker info` nel terminale, l'output dovrebbe indicare che il servizio Docker è in esecuzione.

### <a name="steps"></a>Passaggi
1. Eseguire il pull dell'immagine del contenitore di Jenkins per Service Fabric: `docker pull rapatchi/jenkins:latest`. Questa immagine viene fornita con il plug-in di Jenkins per Service Fabric preinstallato.
1. Eseguire l'immagine del contenitore: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Ottenere l'ID istanza dell'immagine del contenitore. È possibile elencare tutti i contenitori Docker con il comando `docker ps –a`
1. Accedere al portale di Jenkins con questa procedura:

   1. Accedere a una shell di Jenkins dall'host. Usare le prime quattro cifre dell'ID contenitore. Se ad esempio l'ID contenitore è `2d24a73b5964`, usare `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Dalla shell di Jenkins ottenere la password amministratore per l'istanza del contenitore:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Per accedere al dashboard di Jenkins, aprire l'URL seguente in un Web browser: `http://<HOST-IP>:8080`. Usare la password indicata nel passaggio precedente per sbloccare Jenkins.
   1. Facoltativo. Dopo il primo accesso, è possibile creare il proprio account utente e usarlo per i passaggi seguenti oppure è possibile continuare a usare l'account amministratore. Se si crea un utente, è necessario continuare con tale utente.
1. Configurare GitHub per l'interazione con Jenkins seguendo la procedura illustrata in [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generazione di una nuova chiave SSH e aggiunta della chiave all'agente SSH).
   * Usare le istruzioni fornite da GitHub per generare la chiave SSH e aggiungerla all'account GitHub che ospita il repository.
   * Eseguire i comandi specificati nell'articolo indicato in precedenza nella shell Docker di Jenkins, non nell'host.
   * Per accedere alla shell di Jenkins dall'host, eseguire questo comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Assicurarsi che il cluster o il computer in cui è ospitata l'immagine del contenitore Jenkins abbia un indirizzo IP pubblico. Ciò consente all'istanza di Jenkins di ricevere notifiche da GitHub.

Dopo avere configurato Jenkins, continuare con la sezione successiva [Creare e configurare un processo Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Creare e configurare un processo Jenkins

I passaggi descritti in questa sezione mostrano come configurare un processo Jenkins per rispondere alle modifiche in un repository GitHub, recuperare le modifiche e compilarle. Alla fine di questa sezione, si verrà indirizzati ai passaggi finali per configurare il processo per la distribuzione dell'applicazione in un ambiente di sviluppo/test o in un ambiente di produzione. 

1. Nel dashboard di Jenkins fare clic su **New Item** (Nuovo elemento).
1. Immettere un nome per l'elemento, ad esempio **MyJob**. Selezionare **free-style project** (progetto libero) e fare clic su **OK**.
1. Viene aperta la pagina di configurazione del processo (per andare alla configurazione dal dashboard di Jenkins, fare clic sul processo e quindi fare clic su **Configure** (Configura)).

1. Nella scheda **General** (Generale) selezionare la casella associata a **GitHub project** (Progetto GitHub) e specificare l'URL del progetto GitHub. Questo URL ospita l'applicazione Java di Service Fabric da integrare con il flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio `https://github.com/{your-github-account}/service-fabric-java-getting-started`.

1. Nella scheda **Source Code Management** (Gestione del codice sorgente) selezionare **Git**. Specificare l'URL del repository che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio `https://github.com/{your-github-account}/service-fabric-java-getting-started`. È anche possibile specificare il ramo da compilare, ad esempio `/master`.
1. Configurare il repository *GitHub* in modo che comunichi con Jenkins:

   a. Nella pagina del repository GitHub passare a **Settings** (Impostazioni) > **Integrations and Services** (Integrazioni e servizi).

   b. Selezionare **Add Service** (Aggiungi servizio), digitare **Jenkins** e selezionare il **plug-in GitHub per Jenkins**.

   c. Immettere l'URL webhook di Jenkins (per impostazione predefinita, `http://<PublicIPorFQDN>:8081/github-webhook/`). Fare clic su **add/update service** (aggiungi/aggiorna il servizio).

   d. Verrà inviato un evento di test all'istanza di Jenkins. Accanto al webhook in GitHub verrà visualizzato un segno di spunta verde e il progetto verrà compilato.

1. Nella scheda **Build Triggers** (Trigger compilazione) in Jenkins selezionare l'opzione di compilazione da usare. Per questo esempio si vuole attivare una compilazione ogni volta che si verifica un push nel repository, pertanto selezionare **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm). Questa opzione era definita in precedenza **Build when a change is pushed to GitHub** (Compila in caso di push di modifiche in GitHub).
1. Nella scheda **Build** (Compilazione) eseguire una di queste operazioni a seconda che si compili un'applicazione Java o un'applicazione .NET Core:

   * **Per le applicazioni Java:** dall'elenco a discesa **Add build step** (Aggiungi passaggio di compilazione) selezionare **Invoke Gradle Script** (Richiama script Gradle). Fare clic su **Advanced**. Nel menu Advanced (Avanzato) specificare il percorso per **Root build script** (Script di compilazione radice) per l'applicazione. Viene estratto il file build.gradle dal percorso specificato che opera in modo corrispondente. Per l'[applicazione ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter) è: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Azione di compilazione di Jenkins per Service Fabric][build-step]

   * **Per le applicazioni .NET Core:** dall'elenco a discesa **Add build step** (Aggiungi passaggio di compilazione) selezionare **Execute Shell** (Esegui shell). Nella casella di comando visualizzata è necessario prima modificare la directory per il percorso in cui si trova il file build.sh. Dopo avere modificato la directory, è possibile eseguire lo script build.sh e verrà compilata l'applicazione.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     La schermata seguente mostra un esempio dei comandi che vengono usati per compilare l'esempio [CounterService](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) con il nome di processo Jenkins CounterServiceApplication.

      ![Azione di compilazione di Jenkins per Service Fabric][build-step-dotnet]

1. Per configurare Jenkins per la distribuzione dell'app in un cluster di Service Fabric nelle azioni di post-compilazione, è necessario il percorso del certificato del cluster nel contenitore di Jenkins. Scegliere una delle seguenti opzioni a seconda che il contenitore di Jenkins sia in esecuzione all'interno o all'esterno del cluster e prendere nota del percorso del certificato del cluster:

   * **Per Jenkins in esecuzione all'interno del cluster:** è possibile trovare il percorso del certificato tramite ECHO del valore della variabile di ambiente *Certificates_JenkinsOnSF_Code_MyCert_PEM* dall'interno del contenitore.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Per Jenkins in esecuzione all'esterno del cluster:** seguire questi passaggi per copiare il certificato del cluster nel contenitore:
     1. Il certificato deve essere in formato PEM. Se non si dispone di un file PEM, è possibile crearne uno dal file PFX del certificato. Se il file PFX non è protetto da password, eseguire il comando seguente dall'host:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Se il file PFX è protetto da password, includere la password nel parametro `-passin`. Ad esempio:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Per ottenere l'ID contenitore per il contenitore di Jenkins, eseguire `docker ps` dall'host.
     1. Copiare il file PEM nel contenitore con il comando di Docker seguente:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

La procedura è quasi completata. Tenere aperto il processo Jenkins. L'ultima operazione da eseguire consiste nella configurazione delle istruzioni di post-compilazione per distribuire l'applicazione al cluster di Service Fabric:

* Per eseguire la distribuzione in un ambiente di sviluppo o test, seguire i passaggi descritti in [Configurare la distribuzione tramite l'endpoint di gestione del cluster](#configure-deployment-using-cluster-management-endpoint).
* Per eseguire la distribuzione in un ambiente di produzione, seguire i passaggi descritti in [Configurare la distribuzione tramite le credenziali di Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurare la distribuzione tramite l'endpoint di gestione del cluster
Per gli ambienti di sviluppo e test, è possibile usare l'endpoint di gestione del cluster per distribuire l'applicazione. La configurazione dell'azione di post-compilazione con l'endpoint di gestione del cluster per la distribuzione dell'applicazione richiede la quantità minima di configurazione. Se si esegue la distribuzione in un ambiente di produzione, passare direttamente a [Configurare la distribuzione tramite le credenziali di Azure](#configure-deployment-using-azure-credentials) per configurare un'entità servizio di Azure Active Directory da usare durante la distribuzione.    

1. Nel processo Jenkins fare clic sulla scheda **Post-build Actions** (Azioni post-compilazione). 
1. Nell'elenco a discesa **Post-Build Actions** (Azioni post-compilazione) selezionare **Deploy Service Fabric Project** (Distribuisci progetto di Service Fabric). 
1. In **Service Fabric Cluster Configuration** (Configurazione cluster di Service Fabric) selezionare il pulsante di opzione **Fill the Service Fabric Management Endpoint** (Riempi endpoint di gestione di Service Fabric).
1. Per **Management Host** (Host di gestione) immettere l'endpoint di connessione del cluster, ad esempio `{your-cluster}.eastus.cloudapp.azure.com`.
1. Per **Client Key** (Chiave client) e **Client Cert** (Certificato client) immettere il percorso del file PEM nel contenitore di Jenkins, ad esempio `/var/jenkins_home/clustercert.pem` (è stato copiato il percorso del certificato nell'ultimo passaggio di [Creare e configurare un processo Jenkins](#create-and-configure-a-jenkins-job)).
1. In **Application Configuration** (Configurazione applicazione) configurare i campi **Application Name** (Nome applicazione), **Application Type** (Tipo applicazione) e **Path to Application Manifest** (Percorso manifesto dell'applicazione) (relativo).

   ![Azione di post-compilazione di Jenkins per Service Fabric di configurazione dell'endpoint di gestione](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Fare clic su **Verify configuration**. Se la verifica ha esito positivo, fare clic su **Save** (Salva). La pipeline di processo Jenkins è ora completamente configurata. Andare direttamente a [Passaggi successivi](#next-steps) per testare la distribuzione.

## <a name="configure-deployment-using-azure-credentials"></a>Configurare la distribuzione tramite le credenziali di Azure
Per gli ambienti di produzione, è vivamente consigliato configurare le credenziali di Azure per distribuire l'applicazione. Questa sezione descrive come configurare un'entità servizio di Azure Active Directory da usare per distribuire l'applicazione nell'azione di post-compilazione. È possibile assegnare entità servizio ai ruoli nella directory per limitare le autorizzazioni del processo Jenkins. 

Per gli ambienti di sviluppo e test, è possibile configurare le credenziali di Azure o l'endpoint di gestione del cluster per distribuire l'applicazione. Per maggiori dettagli su come configurare un endpoint di gestione del cluster, vedere [Configurare la distribuzione tramite l'endpoint di gestione del cluster](#configure-deployment-using-cluster-management-endpoint).   

1. Per creare un'entità servizio di Azure Active Directory e assegnarvi le autorizzazioni nella sottoscrizione di Azure, seguire i passaggi descritti in [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Prestare attenzione a quanto descritto di seguito:

   * Quando si seguono i passaggi descritti nell'argomento, assicurarsi di copiare e salvare i valori seguenti: *ID applicazione*, *Chiave applicazione*, *ID directory (ID Tenant)* e *ID sottoscrizione*, necessari per configurare le credenziali di Azure in Jenkins.
   * Se non si dispone delle [autorizzazioni richieste](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) per la directory, sarà necessario chiedere a un amministratore di concedere tali autorizzazioni o di creare l'entità servizio per l'utente oppure sarà necessario configurare l'endpoint di gestione per il cluster nelle **azioni di post-compilazione** per il processo in Jenkins.
   * Nella sezione [Creare un'applicazione Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) è possibile immettere qualsiasi URL ben formato nella casella **URL di accesso**.
   * Nella sezione [Assegnare l'applicazione al ruolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) è possibile assegnare all'applicazione il ruolo *Lettore* per il gruppo di risorse per il cluster.

1. Tornando al processo Jenkins, fare clic sulla scheda **Post-build Actions** (Azioni post-compilazione).
1. Nell'elenco a discesa **Post-Build Actions** (Azioni post-compilazione) selezionare **Deploy Service Fabric Project** (Distribuisci progetto di Service Fabric). 
1. In **Service Fabric Cluster Configuration** (Configurazione cluster di Service Fabric) selezionare il pulsante di opzione **Select the Service Fabric Cluster** (Seleziona il cluster di Service Fabric). Fare clic su **Add** (Aggiungi) accanto a **Azure Credentials** (Credenziali di Azure). Fare clic su **Jenkins** per selezionare il provider di credenziali di Jenkins.
1. Nella finestra del provider di credenziali di Jenkins selezionare **Microsoft Azure Service Principal** (Entità servizio di Microsoft Azure) dall'elenco a discesa **Kind** (Tipologia).
1. Usare i valori salvati durante la configurazione dell'entità servizio nel passaggio 1 per configurare i campi seguenti:

   * **ID client**: *ID applicazione*
   * **Segreto client**: *Chiave applicazione*
   * **ID tenant**: *ID directory*
   * **ID sottoscrizione**: *ID sottoscrizione*
1. Immettere un **ID** descrittivo da usare per selezionare le credenziali in Jenkins e una breve **Descrizione**. Quindi fare clic su **Verify Service Principal** (Verifica entità servizio). Se la verifica ha esito positivo, fare clic su **Add** (Aggiungi).

   ![Immissione delle credenziali di Azure di Jenkins per Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Tornando in **Service Fabric Cluster Configuration** (Configurazione cluster di Service Fabric), assicurarsi che le nuove credenziali siano selezionate per **Azure Credentials** (Credenziali di Azure). 
1. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare il gruppo di risorse del cluster in cui si vuole distribuire l'applicazione.
1. Nell'elenco a discesa **Service Fabric** selezionare il cluster in cui si vuole distribuire l'applicazione.
1. Per **Client Key** (Chiave client) e **Client Cert** (Certificato client), immettere il percorso del file PEM nel contenitore di Jenkins. Ad esempio, `/var/jenkins_home/clustercert.pem`. 
1. In **Application Configuration** (Configurazione applicazione) configurare i campi **Application Name** (Nome applicazione), **Application Type** (Tipo applicazione) e **Path to Application Manifest** (Percorso manifesto dell'applicazione) (relativo).
    ![Azione di post-compilazione di Jenkins per Service Fabric di configurazione delle credenziali di Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Fare clic su **Verify configuration**. Se la verifica ha esito positivo, fare clic su **Save** (Salva). La pipeline di processo Jenkins è ora completamente configurata. Continuare con [Passaggi successivi](#next-steps) per testare la distribuzione.

## <a name="troubleshooting-the-jenkins-plugin"></a>Risoluzione dei problemi del plug-in Jenkins

Se si rilevano bug con i plug-in Jenkins, segnalare un problema in [Jenkins JIRA](https://issues.jenkins-ci.org/) per il componente specifico.

## <a name="next-steps"></a>Passaggi successivi
GitHub e Jenkins sono ora configurati. Prendere in considerazione alcune modifiche di esempio nel progetto `reliable-services-actor-sample/Actors/ActorCounter` per il fork del repository https://github.com/Azure-Samples/service-fabric-java-getting-started. Eseguire il push delle modifiche al ramo `master` remoto o a qualsiasi ramo configurato per l'uso. Viene così attivato il processo Jenkins `MyJob` configurato. Le modifiche vengono recuperate da GitHub e compilate e l'applicazione viene distribuita nel cluster specificato nelle azioni di post-compilazione.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

