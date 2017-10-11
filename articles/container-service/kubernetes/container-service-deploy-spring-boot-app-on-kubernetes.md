---
title: Distribuire un'app Spring Boot su Kubernetes nel servizio contenitore di Azure | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato la procedura per la distribuzione di un'applicazione Spring Boot in un cluster Kubernetes in Microsoft Azure.
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.openlocfilehash: 7f726436b2d459b8c16abb02e07de099abfd8974
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-a-spring-boot-application-on-a-kubernetes-cluster-in-the-azure-container-service"></a>Distribuire un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure

**[Spring Framework]** è uno dei framework open source più diffusi e consente agli sviluppatori Java di creare applicazioni Web, per dispositivi mobili e per le API. Questa esercitazione usa un'app di esempio creata con [Spring Boot], un approccio basato su convenzioni per l'uso di Spring per iniziare rapidamente a creare app.

**[Kubernetes]** e **[Docker]** sono soluzioni open source che consentono agli sviluppatori di automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in esecuzione nei contenitori.

Questa esercitazione illustra in modo dettagliato la combinazione di queste due tecnologie open source note per sviluppare e distribuire un'applicazione Spring Boot in Microsoft Azure. In particolare, è possibile usare *[Spring Boot]* per lo sviluppo dell'applicazione, *[Kubernetes]* per la distribuzione dei contenitori e il [servizio contenitore di Azure] per l'hosting dell'applicazione.

### <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure; se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN] oppure iscriversi per ottenere un [account Azure gratuito].
* [Interfaccia della riga di comando di Azure].
* Un [Java Developer Kit (JDK)] aggiornato.
* Lo strumento di compilazione [Maven] di Apache (versione 3).
* Un client [Git].
* Un client [Docker].

> [!NOTE]
>
> A causa dei requisiti di virtualizzazione di questa esercitazione, non è possibile seguire la procedura illustrata in questo articolo in una macchina virtuale. È necessario usare un computer fisico in cui sono abilitate le funzionalità di virtualizzazione.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Creare l'app Web introduttiva di Spring Boot in Docker

La procedura seguente illustra come creare un'applicazione Web di Spring Boot e come testarla in locale.

1. Aprire un prompt dei comandi e creare una directory locale in cui contenere l'applicazione, quindi passare a tale directory. Ad esempio:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - o-
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clonare il progetto di esempio [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker) nella directory.
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Passare alla directory del progetto completato.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Usare Maven per compilare ed eseguire l'app di esempio.
   ```
   mvn package spring-boot:run
   ```

1. Testare l'app Web passando a http://localhost:8080 oppure eseguendo il comando `curl` seguente:
   ```
   curl http://localhost:8080
   ```

1. Dovrebbe essere visualizzato il messaggio seguente: **Hello Docker World**

   ![Esplorare l'app di esempio in locale][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Creare un registro contenitori di Azure usando l'interfaccia della riga di comando di Azure

1. Aprire un prompt dei comandi.

1. Accedere all'account di Azure:
   ```azurecli
   az login
   ```

1. Creare un gruppo di risorse per le risorse di Azure usate in questa esercitazione.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Creare un registro contenitori privato di Azure nel gruppo di risorse. L'esercitazione effettua il push dell'app di esempio come immagine Docker in questo registro nei passaggi successivi. Sostituire `wingtiptoysregistry` con un nome univoco per il registro.
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes--location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry"></a>Effettuare il push dell'app nel registro contenitori

1. Passare alla directory di configurazione dell'installazione di Maven (impostazione predefinita: ~/.m2/ or C:\Users\nomeutente\.m2) e aprire il file *settings.xml* con un editor di testo.

1. Recuperare la password per il registro contenitori dall'interfaccia della riga di comando di Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   ```json
   {
  "name": "password",
  "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Aggiungere l'ID e la password del registro contenitori di Azure a una nuova raccolta `<server>` nel file *settings.xml*.
I valori `id` e `username` corrispondono al nome del registro. Usare il valore `password` del comando precedente, senza virgolette.

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Passare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, "*C:\SpringBoot\gs-spring-boot-docker\complete*" o "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") e aprire il file *pom.xml* con un editor di testo.

1. Aggiornare la raccolta `<properties>` nel file *pom.xml* con il valore del server di accesso per il registro contenitori di Azure.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Aggiornare la raccolta `<plugins>` nel file *pom.xml* in modo che `<plugin>` contenga l'indirizzo del server di accesso e il nome del registro per il registro contenitori di Azure.

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Passare alla directory del progetto completato per l'applicazione Spring Boot ed eseguire il comando seguente per creare il contenitore Docker ed effettuare il push dell'immagine nel registro:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
>  Quando Maven effettua il push dell'immagine in Azure, è possibile che venga visualizzato un messaggio di errore simile al seguente:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Se viene visualizzato questo errore, accedere ad Azure dalla riga di comando di Docker.
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Effettuare quindi il push del contenitore:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`

## <a name="create-a-kubernetes-cluster-on-acs-using-the-azure-cli"></a>Creare un cluster Kubernetes nel servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure

1. Creare un cluster Kubernetes nel servizio contenitore di Azure. Il comando seguente crea un cluster *kubernetes* nel gruppo di risorse *wingtiptoys-kubernetes* con *wingtiptoys-containerservice* come nome del cluster e *wingtiptoys-kubernetes* come prefisso DNS:
   ```azurecli
   az acs create --orchestrator-type=kubernetes --resource-group=wingtiptoys-kubernetes \ 
    --name=wingtiptoys-containerservice --dns-prefix=wingtiptoys-kubernetes
   ```
   Il completamento di questo comando può richiedere alcuni minuti.

1. Installare `kubectl` usando l'interfaccia della riga di comando di Azure. È possibile che gli utenti Linux debbano aggiungere al comando il prefisso `sudo`, perché distribuisce l'interfaccia della riga di comando di Kubernetes in `/usr/local/bin`.
   ```azurecli
   az acs kubernetes install-cli
   ```

1. Scaricare le informazioni sulla configurazione del cluster, in modo da consentire la gestione del cluster dall'interfaccia Web di Kubernetes e `kubectl`. 
   ```azurecli
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes  \ 
    --name=wingtiptoys-containerservice
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Distribuire l'immagine nel cluster Kubernetes

Questa esercitazione distribuisce l'app usando `kubectl`, quindi consente di esplorare la distribuzione tramite l'interfaccia Web di Kubernetes.

### <a name="deploy-with-the-kubernetes-web-interface"></a>Eseguire la distribuzione con l'interfaccia Web di Kubernetes

1. Aprire un prompt dei comandi.

1. Aprire il sito Web di configurazione per il cluster Kubernetes nel browser predefinito:
   ```
   az acs kubernetes browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-containerservice
   ```

1. All'apertura del sito Web di configurazione di Kubernetes nel browser, fare clic sul collegamento **deploy a containerized app** (Distribuire un'app inclusa in contenitori):

   ![Sito Web di configurazione di Kubernetes][KB01]

1. Quando viene visualizzata la pagina **Deploy a containerized app** (Distribuire un'app inclusa in contenitori), specificare le opzioni seguenti:

   a. Selezionare **Specify app details below** (Specificare più avanti i dettagli dell'app).

   b. Immettere il nome dell'applicazione Spring Boot per **App name** (Nome app), ad esempio: "*gs-spring-boot-docker*".

   c. Immettere il server di accesso e l'immagine del contenitore dai passaggi precedenti in **Container image** (Immagine del contenitore), ad esempio: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*".

   d. Scegliere **External** (Esterno) per **Service** (Servizio).

   e. Specificare le porte esterne ed interne nelle caselle di testo **Port** (Porta) e **Target port** (Porta di destinazione).

   ![Sito Web di configurazione di Kubernetes][KB02]


1. Fare clic su **Deploy** (Distribuisci) per distribuire il contenitore.

   ![Distribuire un contenitore][KB05]

1. Al termine della distribuzione dell'applicazione, l'applicazione Spring Boot verrà elencata in **Services** (Servizi).

   ![Servizi Kubernetes][KB06]

1. Se si fa clic sul collegamento per **External endpoints** (Endpoint esterni), è possibile visualizzare l'applicazione Spring Boot in esecuzione in Azure.

   ![Servizi Kubernetes][KB07]

   ![Esplorare l'app di esempio in Azure][SB02]


### <a name="deploy-with-kubectl"></a>Eseguire la distribuzione con kubectl

1. Aprire un prompt dei comandi.

1. Eseguire il contenitore nel cluster Kubernetes usando il comando `kubectl run`. Specificare un nome di servizio per l'app in Kubernetes e il nome completo dell'immagine. ad esempio:
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   In questo comando:

   * Il nome del contenitore `gs-spring-boot-docker` viene specificato immediatamente dopo il comando `run`.

   * Il parametro `--image` specifica il nome combinato del server di accesso e dell'immagine come `wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`.

1. Esporre esternamente il cluster Kubernetes usando il comando `kubectl expose`. Specificare il nome del servizio, la porta TCP pubblica usata per accedere all'app e la porta di destinazione interna su cui è in ascolto l'app. ad esempio:
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   In questo comando:

   * Il nome del contenitore `gs-spring-boot-docker` viene specificato immediatamente dopo il comando `expose deployment`.

   * Il parametro `--type` specifica che il cluster usa il bilanciamento del carico

   * Il parametro `--port` specifica la porta TCP pubblica, ovvero 80. Si accede all'app tramite questa porta.

   * Il parametro `--target-port` specifica la porta TCP interna, ovvero 8080. Il servizio di bilanciamento del carico inoltra le richieste all'app su questa porta.

1. Dopo la distribuzione dell'app nel cluster, eseguire query sull'indirizzo IP esterno e aprirlo nel Web browser:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=${namespace}
   ```

   ![Esplorare l'app di esempio in Azure][SB02]


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Spring Boot in Azure, vedere gli articoli seguenti:

* [Distribuire un'applicazione Spring Boot nel servizio app di Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure](container-service-deploy-spring-boot-app-on-linux.md)

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per altre informazioni sul progetto di esempio di Spring Boot in Docker, vedere [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker).

I collegamenti seguenti forniscono informazioni aggiuntive sulla creazione di applicazioni Spring Boot:

* Per altre informazioni sulla creazione di una semplice applicazione Spring Boot, vedere Spring Initializr all'indirizzo https://start.spring.io/.

I collegamenti seguenti forniscono informazioni aggiuntive sull'uso di Kubernetes con Azure:

* [Introduzione a un cluster Kubernetes nel servizio contenitore](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough)
* [Uso dell'interfaccia utente Web Kubernetes con il servizio contenitore di Azure](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-ui)

Per altre informazioni sull'uso dell'interfaccia della riga di comando di Kubernetes, vedere la Guida dell'utente di **kubectl** all'indirizzo <https://kubernetes.io/docs/user-guide/kubectl/>.

Il sito Web Kubernetes include alcuni articoli relativi all'uso delle immagini nei registri privati:

* [Configuring Service Accounts for Pods] (Configurazione degli account del servizio per i pod)
* [Namespaces] (Spazi dei nomi)
* [Pulling an Image from a Private Registry] (Effettuare il pull di un'immagine da un registro privato)

Per altri esempi sull'uso delle immagini personalizzate di Docker con Azure, vedere [Uso di un'immagine Docker personalizzata per App Web di Azure in Linux].

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[servizio contenitore di Azure]: https://azure.microsoft.com/services/container-service/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Uso di un'immagine Docker personalizzata per App Web di Azure in Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker (Introduzione a Spring Boot in Docker)
[Spring Framework]: https://spring.io/ (Framework di Spring)
[Configuring Service Accounts for Pods]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ (Configurazione degli account del servizio per i pod)
[Namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/ (Spazi dei nomi)
[Pulling an Image from a Private Registry]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/ (Effettuare il pull di un'immagine da un registro privato)

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR04.png

[KB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB01.png
[KB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB02.png
[KB03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB03.png
[KB04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB04.png
[KB05]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB05.png
[KB06]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB06.png
[KB07]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB07.png
