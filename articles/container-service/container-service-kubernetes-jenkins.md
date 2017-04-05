---
title: CI/CD di Jenkins con Kubernetes nel servizio contenitore di Azure | Microsoft Docs
description: Come automatizzare un processo CI/CD con Jenkins per distribuire e aggiornare un&quot;app dei contenitori in Kubernetes nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: Docker, contenitori, Kubernetes, Azure, Jenkins
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 3d206ebb6deeaa40f8e792ec12304c99c0abe684
ms.lasthandoff: 03/29/2017


---

# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Integrazione di Jenkins con il servizio contenitore di Azure e Kubernetes 
Questa esercitazione illustra il processo per impostare l'integrazione continuata di un'applicazione multi-contenitore nel servizio contenitore di Azure Kubernetes usando la piattaforma Jenkins. Il flusso di lavoro aggiorna l'immagine del contenitore in Docker Hub e i podcast Kubernetes implementando la distribuzione. 

## <a name="high-level-process"></a>Processo principale
I passaggi di base descritti in questo articolo sono: 
- Installare un cluster Kubernetes nel servizio contenitore
- Impostare Jenkins e configurare l'accesso al servizio contenitore
- Creare un flusso di lavoro Jenkins
- Testare il processo CI/CD end-to-end

## <a name="install-a-kubernetes-cluster"></a>Installare un cluster Kubernetes
    
Distribuire il cluster Kubernetes nel servizio contenitore di Azure seguendo questi passaggi. La documentazione completa è disponibile [qui](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Passaggio 1: Creare un gruppo di risorse
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Passaggio 2: Distribuire il cluster
> [!NOTE]
> Le operazioni seguenti richiedono una chiave pubblica SSH locale archiviata nella cartella ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \ 
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \ 
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Impostare Jenkins e configurare l'accesso al servizio contenitore

### <a name="step-1-install-jenkins"></a>Passaggio 1: Installare Jenkins
1. Creare una macchina virtuale di Azure con Ubuntu 16.04 LTS. 
2. Installare Jenkins tramite queste [istruzioni](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu).
3. Un'esercitazione più dettagliata è disponibile all'indirizzo [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
4. Aggiornare il gruppo di sicurezza di rete di Azure per autorizzare la porta 8080 e quindi esplorare l'indirizzo IP pubblico alla porta 8080 per gestire Jenkins nel browser.
5. La password amministratore iniziale di Jenkins viene archiviata in /var/lib/jenkins/secrets/initialAdminPassword.
6. Installare Docker nel computer Jenkins tramite queste [istruzioni](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). In questo modo è possibile eseguire comandi Docker nei processi Jenkins.
7. Configurare le autorizzazioni di Docker per consentire a Jenkins di accedere all'endpoint.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Installare l'interfaccia della riga di comando `kubectl` in Jenkins. Altre informazioni sono disponibili leggendo l'articolo sull'[installazione e la configurazione di kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Passaggio 2: Impostare l'accesso al cluster Kubernetes

> [!NOTE]
> Esistono diversi approcci per eseguire la procedura seguente. Usare l'approccio che si ritiene più semplice.
>

1. Copiare il file di configurazione `kubectl` nel computer Jenkins.

    ```bash
    export KUBE_MASTER=<your_cluster_master_fqdn>
        
    sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config user@<your_jenkins_server>:~/.kube/config
        
    sudo ssh user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config

    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config
        
    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo cp /home/user/.kube/config /var/lib/jenkins/config
    ```
        
2. Da Jenkins, verificare che il cluster Kubernetes sia accessibile.
    

## <a name="create-a-jenkins-workflow"></a>Creare un flusso di lavoro Jenkins

### <a name="prerequisites"></a>Prerequisiti

- Account GitHub per il repository di codice.
- Account Docker Hub per archiviare e aggiornare le immagini.
- Applicazione in contenitore, ricompilabile e aggiornabile. È possibile usare questa applicazione contenitore di esempio scritta in Golang: https://github.com/chzbrgr71/go-web 

> [!NOTE]
> Eseguire i passaggi seguenti nel proprio account GitHub. È possibile clonare il repository precedente, ma è necessario usare il proprio account per configurare i webhook e l'accesso a Jenkins.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Passaggio 1: Distribuire la versione 1 iniziale dell'applicazione
1. Compilare l'applicazione dal computer per lo sviluppo con i comandi seguenti. Sostituire `myrepo` con la propria.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Inviare l'immagine a Docker Hub.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Distribuire nel cluster Kubernetes.
    
    > [!NOTE] 
    > Modificare il file `go-web.yaml` per aggiornare l'immagine del contenitore e il repository.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Passaggio 2: Configurare il sistema Jenkins
1. Fare clic su **Manage Jenkins (Gestisci Jenkins)** > **Configure System (Configura sistema)**.
2. In **GitHub**selezionare **Add GitHub Server** (Aggiungi server GitHub).
3. Lasciare il campo **API URL** come predefinito.
4. In **Credenziali**, aggiungere una credenziale Jenkins usando **Secret text** (Testo segreto). Si consiglia di usare i token di accesso a GitHub personali, configurati nelle impostazioni dell'account utente GitHub. Altre informazioni a riguardo sono disponibili [qui.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Fare clic su **Connessione di test** per assicurarsi che la configurazione sia stata eseguita correttamente.
6. In **Proprietà globali** aggiungere una variabile di ambiente `DOCKER_HUB` e indicare la password di Docker Hub. Questa operazione è utile per questa demo, ma uno scenario di produzione richiederà un approccio più sicuro.
7. Salvare.

![Accesso GitHub a Jenkins](media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Passaggio 3: Creare il flusso di lavoro Jenkins
1. Creare un elemento Jenkins.
2. Specificare un nome (ad esempio, "go-web") e selezionare **Freestyle Project** (Progetto Freestyle). 
3. Controllare **GitHub project** (Progetto GitHub) e indicare l'URL del repository di GitHub.
4. In **Source Code Management** (Gestione codice sorgente) fornire l'URL e le credenziali del repository di GitHub. 
5. Aggiungere un'**istruzione di compilazione** di tipo **Esegui shell** e usare il seguente testo:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Aggiungere un'altra **istruzione di compilazione** di tipo **Esegui shell** e usare il seguente testo:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Passaggi di compilazione in Jenkins](media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Salvare l'elemento Jenkins e testarlo con **Build Now** (Compila ora).

### <a name="step-4-connect-github-webhook"></a>Passaggio 4: Connettere il webhook GitHub
1. Nell'elemento di Jenkins creato fare clic su **Configura**.
2. In **Build Triggers** (Trigger di compilazione), selezionare l'opzione **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm) e **salvare**. Verrà automaticamente configurato il webhook GitHub.
3. Nel repository di GitHub per go-web, fare clic su **Impostazioni > Webhook**.
4. Verificare che l'URL del webhook Jenkins sia stato aggiunto correttamente. L'URL deve terminare con "github-webhook".

![Configurazione del webhook Jenkins](media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Testare il processo CI/CD end-to-end

1. Aggiornare il codice per il repository ed eseguire il push/sincronizzazione con il repository di GitHub.
2. Dalla console Jenkins, controllare la **Build History** (Cronologia compilazione) e verificare l'esecuzione corretta del processo. Visualizzazione l'output della console per i dettagli.
3. Da Kubernetes, visualizzare i dettagli della distribuzione aggiornata:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Passaggi successivi

- Distribuire un Registro di sistema del contenitore di Azure e archiviare le immagini in un repository sicuro. Vedere la [documentazione sul Registro di sistema del contenitore di Azure](https://docs.microsoft.com/azure/container-registry).
- Creare un flusso di lavoro più complesso che includa la distribuzione affiancata e i test automatizzati in Jenkins.
- Per ulteriori informazioni su CI/CD con Jenkins e Kubernetes, vedere il [blog su Jenkins](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).

