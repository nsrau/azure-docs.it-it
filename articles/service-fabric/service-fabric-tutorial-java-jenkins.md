---
title: Configurare l'integrazione continua/distribuzione continua per un'applicazione Java di Azure Service Fabric | Microsoft Docs
description: In questa esercitazione si apprenderà come configurare l'integrazione continua con Jenkins per distribuire un'applicazione Java di Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: bd986b8741b55a10018f7400c9415e7aedfbf119
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-set-up-a-jenkins-environment-with-service-fabric"></a>Esercitazione: Configurare un ambiente Jenkins con Service Fabric
Questa è la quinta di una serie di esercitazioni. Illustra come usare Jenkins per distribuire gli aggiornamenti nell'applicazione. In questa esercitazione, il plug-in Jenkins per Service Fabric viene usato insieme a un repository Github che ospita l'applicazione Voting per distribuire l'applicazione in un cluster. 

Nella quinta parte della serie si apprenderà come:
> [!div class="checklist"]
> * Distribuire il contenitore di Jenkins per Service Fabric nel computer
> * Configurare l'ambiente Jenkins per la distribuzione in Service Fabric
> * Aggiornare l'applicazione

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Compilare un'applicazione Reliable Services di Service Fabric per Java](service-fabric-tutorial-create-java-app.md)
> * [Distribuire l'applicazione ed eseguirne il debug in un cluster locale](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Distribuire l'applicazione in un cluster di Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-java-elk.md)
> * Configurare l'integrazione continua/distribuzione continua


## <a name="prerequisites"></a>prerequisiti
- Installare Git nel computer locale dalla [pagina di download di Git](https://git-scm.com/downloads). Per altre informazioni su Git, vedere la [documentazione](https://git-scm.com/docs).
- Avere una conoscenza pratica di [Jenkins](https://jenkins.io/).
- Creare un account [GitHub](https://github.com/) e saper usare GitHub.
- Installare [Docker](https://www.docker.com/community-edition) nel computer in uso.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Eseguire il pull dell'immagine del contenitore di Jenkins per Service Fabric e distribuirla
È possibile configurare Jenkins all'interno o all'esterno di un cluster di Service Fabric. Le istruzioni seguenti illustrano come configurarlo all'esterno di un cluster usando un'immagine Docker fornita. È tuttavia anche possibile usare un ambiente di compilazione Jenkins preconfigurato. L'immagine del contenitore seguente viene installata con il plug-in di Service Fabric ed è immediatamente pronta per l'uso con Service Fabric. 

1. Eseguire il pull dell'immagine del contenitore di Jenkins per Service Fabric: ``docker pull rapatchi/jenkins:v10``. Questa immagine viene fornita con il plug-in di Jenkins per Service Fabric preinstallato.

2. Eseguire l'immagine del contenitore con il percorso in cui si trovano i certificati nel computer locale

    ```bash
    docker run -itd -p 8080:8080 -v /Users/suhuruli/Documents/Work/Samples/service-fabric-java-quickstart/AzureCluster:/tmp/myCerts rapatchi/jenkins:v10
    ```

3. Ottenere l'ID istanza dell'immagine del contenitore. È possibile elencare tutti i contenitori Docker con il comando ``docker ps –a``

4. Recuperare la password dell'istanza di Jenkins eseguendo questo comando:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Se l'ID contenitore è 2d24a73b5964, usare 2d24.
    * Questa password è necessaria per l'accesso al dashboard di Jenkins dal portale ovvero ``http://<HOST-IP>:8080``.
    * Dopo il primo accesso è possibile creare il proprio account utente o usare l'account amministratore.
    
5. Configurare GitHub per l'interazione con Jenkins seguendo la procedura illustrata in [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generazione di una nuova chiave SSH e aggiunta della chiave all'agente SSH). Poiché i comandi vengono eseguiti dal contenitore Docker, seguire le istruzioni per l'ambiente Linux. 
    * Usare le istruzioni fornite da GitHub per generare la chiave SSH. Aggiungere quindi la chiave SSH all'account GitHub che ospita il repository.
    * Eseguire i comandi specificati nell'articolo indicato in precedenza nella shell Docker di Jenkins, non nell'host.
    * Per accedere alla shell di Jenkins dall'host, eseguire questi comandi:

    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

    Assicurarsi che il cluster o il computer in cui è ospitata l'immagine del contenitore Jenkins abbia un indirizzo IP pubblico. La presenza di un indirizzo IP pubblico consente all'istanza di Jenkins di ricevere notifiche da GitHub.    

## <a name="create-and-configure-a-jenkins-job"></a>Creare e configurare un processo Jenkins

1. Se non è disponibile un repository che può essere usato per ospitare il progetto Voting in Github, crearne uno. Il repository verrà chiamato **dev_test** nella parte restante di questa esercitazione.

2. Creare un **nuovo elemento** dal dashboard Jenkins.

3. Immettere un nome per l'elemento, ad esempio **MyJob**. Selezionare **free-style project** (progetto libero) e fare clic su **OK**.

4. Passare quindi alla pagina del processo e fare clic su **Configure** (Configura).

   a. Nella sezione generale selezionare la casella di controllo associata a **GitHub project** (Progetto GitHub) e specificare l'URL del progetto GitHub. Questo URL ospita l'applicazione Java di Service Fabric da integrare con il flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio ``https://github.com/testaccount/dev_test``.

   b. Nella sezione **Source Code Management** (Gestione del codice sorgente) selezionare **Git**. Specificare l'URL del repository che ospita l'applicazione Java di Service Fabric che si vuole integrare nel flusso di integrazione continua e di distribuzione continua di Jenkins, ad esempio *https://github.com/testaccount/dev_test.git*. Qui è anche possibile specificare il ramo da compilare, ad esempio **/master**.

5. Configurare *GitHub*, che ospita il repository, in modo che possa comunicare con Jenkins. Seguire questa procedura:

   a. Passare alla pagina del repository GitHub. Passare a **Settings** (Impostazioni)  > **Integrations and Services** (Integrazioni e servizi).

   b. Selezionare **Add Service** (Aggiungi servizio), digitare **Jenkins** e selezionare il **plug-in GitHub per Jenkins**.

   c. Immettere l'URL webhook di Jenkins (per impostazione predefinita, ``http://<PublicIPorFQDN>:8081/github-webhook/``). Fare clic su **add/update service** (aggiungi/aggiorna il servizio).

   d. Verrà inviato un evento di test all'istanza di Jenkins. Accanto al webhook in GitHub verrà visualizzato un segno di spunta verde e il progetto verrà compilato.

   ![Configurazione di Jenkins per Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

6. Nella sezione **Build Triggers** (Trigger compilazione) selezionare l'opzione di compilazione da usare. Per questo esempio è necessario attivare una compilazione a ogni push nel repository. Selezionare quindi l'opzione **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm).

7. Nella sezione **Build** (Compilazione) selezionare l'opzione **Add build step** (Richiama script Gradle) dall'elenco a discesa **Invoke Gradle Script** (Aggiungi passaggio di compilazione). Nel widget visualizzato aprire il menu Advanced (Avanzato) e specificare il percorso per **Root build script** (Script di compilazione radice) per l'applicazione. Viene estratto il file build.gradle dal percorso specificato che opera in modo corrispondente.

    ![Azione di compilazione di Jenkins per Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)
  
8. Nell'elenco a discesa **Post-Build Actions** (Azioni post-compilazione) selezionare **Deploy Service Fabric Project** (Distribuisci progetto di Service Fabric). In questa finestra è necessario specificare i dettagli del cluster in cui verrà distribuita l'applicazione di Service Fabric compilata con Jenkins. Il percorso del certificato è la posizione in cui è stato montato il volume (/tmp/myCerts). 
   
    È anche possibile specificare dettagli aggiuntivi usati per distribuire l'applicazione. Vedere lo screenshot seguente per un esempio di dettagli dell'applicazione:

    ![Azione di compilazione di Jenkins per Service Fabric](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > In questo caso il cluster potrebbe essere lo stesso che ospita l'applicazione contenitore Jenkins, nel caso si usi Service Fabric per distribuire l'immagine del contenitore Jenkins.
    >

## <a name="update-your-existing-application"></a>Aggiornare l'applicazione esistente 

1. Aggiornare il titolo del contenuto HTML nel file *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* con **Esempio di Voting di Service Fabric v2**. 

    ```html 
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

2. Aggiornare **ApplicationTypeVersion** e **ServiceManifestVersion** alla versione **2.0.0** nel file *Voting/VotingApplication/ApplicationManifest.xml*. 

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>      
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>      
    </ApplicationManifest>
    ```

3. Aggiornare il campo **Version** di **ServiceManifest** e il campo **Version** del tag **CodePackage** nel file *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* alla versione **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

4. Per inizializzare un processo Jenkins che esegue un aggiornamento dell'applicazione, effettuare il push delle nuove modifiche nel repository Github. 

5. In Service Fabric Explorer fare clic sull'elenco a discesa **Applications** (Applicazioni). Per visualizzare lo stato dell'aggiornamento, fare clic sulla scheda **Upgrades in Progress** (Aggiornamenti in corso).

    ![Aggiornamento in corso](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

6. Se si accede a **http://\<IP Host>: 8080**, l'applicazione Voting è ora in esecuzione con la piena funzionalità. 

    ![App Voting locale](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire il contenitore di Jenkins per Service Fabric nel computer
> * Configurare l'ambiente Jenkins per la distribuzione in Service Fabric
> * Aggiornare l'applicazione

* Fare riferimento ad altri [esempi di Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)