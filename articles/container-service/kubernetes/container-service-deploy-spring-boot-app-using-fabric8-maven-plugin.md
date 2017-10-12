---
title: Distribuire un'app Spring Boot con il plug-in Maven Fabric8
description: Questa esercitazione illustra in modo dettagliato la procedura per la distribuzione di un'applicazione Spring Boot su Microsoft Azure tramite il plug-in Fabric8 per Apache Maven.
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: d368e71866406f6011c5cfa75eba13461e8e4ca4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Distribuire un'app Spring Boot con il plug-in Maven Fabric8

**[Fabric8]**  è una soluzione open source basata su  **[Kubernetes]** che consente agli sviluppatori di creare applicazioni in contenitori Linux.

Questa esercitazione illustra l'uso del plug-in Fabric8 per Maven per sviluppare e distribuire un'applicazione in un host Linux nel [servizio contenitore di Azure].

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura di questa esercitazione, sono necessari i prerequisiti seguenti:

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
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   - o-
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Clonare il progetto di esempio [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker) nella directory.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Passare alla directory del progetto completato. Ad esempio:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   - o-
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Usare Maven per compilare ed eseguire l'app di esempio.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Testare l'app Web passando a http://localhost:8080 oppure eseguendo il comando `curl` seguente:
   ```shell
   curl http://localhost:8080
   ```

   Dovrebbe essere visualizzato il messaggio **Hello Docker World**.

   ![Esplorare l'applicazione di esempio localmente][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Kubernetes e creare un gruppo di risorse di Azure usando l'interfaccia della riga di comando di Azure

1. Aprire un prompt dei comandi.

1. Accedere al proprio account di Azure digitando il comando seguente:
   ```azurecli
   az login
   ```
   Seguire le istruzioni per completare il processo di accesso
   
   Nell'interfaccia della riga di comando di Azure verrà visualizzato un elenco degli account, ad esempio:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Se non si dispone ancora dell'interfaccia della riga di comando di Kubernetes (`kubectl`) installata, è possibile installarla tramite l'interfaccia della riga di comando di Azure, ad esempio:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > È possibile che gli utenti Linux debbano aggiungere al comando il prefisso `sudo`, perché distribuisce l'interfaccia della riga di comando di Kubernetes in `/usr/local/bin`.
   >
   > Se si dispone già di `kubectl` installato e la versione di `kubectl` è obsoleta, potrebbe essere visualizzato un messaggio di errore simile all'esempio seguente quando si tenta di completare la procedura descritta più avanti in questo articolo:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > In questo caso, sarà necessario reinstallare `kubectl` per aggiornare la versione in uso.
   >

1. Creare un gruppo di risorse per le risorse di Azure che verranno usate in questa esercitazione, ad esempio:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Dove:  
      * *wingtiptoys-kubernetes* è un nome univoco per il gruppo di risorse  
      * *westeurope* è una posizione geografica appropriata per l'applicazione  

   Nell'interfaccia della riga di comando di Azure verranno visualizzati i risultati della creazione del gruppo di risorse, ad esempio:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Creare un cluster Kubernetes usando l'interfaccia della riga di comando di Azure

1. Creare un cluster Kubernetes nel nuovo gruppo di risorse, ad esempio:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Dove:  
      * *wingtiptoys kubernetes* è il nome del gruppo di risorse creato in precedenza in questo articolo  
      * *wingtiptoys-cluster* è un nome univoco per il cluster Kubernetes
      * *wingtiptoys* è un nome DNS univoco per l'applicazione

   Nell'interfaccia della riga di comando di Azure verranno visualizzati i risultati della creazione del cluster, ad esempio:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Scaricare le credenziali per il nuovo cluster Kubernetes, ad esempio:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Verificare la connessione con il comando seguente:
   ```shell 
   kubectl get nodes
   ```

   Verrà visualizzato un elenco di nodi e stati, come nell'esempio seguente:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Creare un registro contenitori di Azure privato usando l'interfaccia della riga di comando di Azure

1. Creare un registro contenitori di Azure privato nel gruppo di risorse per ospitare l'immagine di Docker, ad esempio:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Dove:  
      * *wingtiptoys kubernetes* è il nome del gruppo di risorse creato in precedenza in questo articolo  
      * *wingtiptoysregistry* è un nome univoco per il registro privato
      * *westeurope* è una posizione geografica appropriata per l'applicazione  

   Nell'interfaccia della riga di comando di Azure verranno visualizzati i risultati della creazione del registro, ad esempio:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. Recuperare la password per il registro contenitori dall'interfaccia della riga di comando di Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   Nell'interfaccia della riga di comando di Azure verrà visualizzata la password del registro, ad esempio:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Passare alla directory di configurazione dell'installazione di Maven (impostazione predefinita: ~/.m2/ or C:\Users\nomeutente\.m2) e aprire il file *settings.xml* con un editor di testo.

1. Aggiungere l'URL, il nome utente e la password di Registro contenitori di Azure a una nuova raccolta `<server>` nel file *settings.xml*.
I valori `id` e `username` corrispondono al nome del registro. Usare il valore `password` del comando precedente, senza virgolette.

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Passare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, "*C:\SpringBoot\gs-spring-boot-docker\complete*" o "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*") e aprire il file *pom.xml* con un editor di testo.

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
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Passare alla directory del progetto completato per l'applicazione Spring Boot ed eseguire il comando Maven seguente per creare il contenitore Docker ed effettuare il push dell'immagine nel registro:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Maven visualizzerà i risultati della compilazione, ad esempio:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Configurare l'app Spring Boot per usare il plug-in Maven Fabric8

1. Passare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, "*C:\SpringBoot\gs-spring-boot-docker\complete*" o "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*") e aprire il file *pom.xml* con un editor di testo.

1. Aggiornare la raccolta `<plugins>` nel file *pom.xml* per aggiungere il plug-in Maven Fabric8:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Passare alla directory principale di origine per l'applicazione Spring Boot (ad esempio: "*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*" o "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*") e creare una nuova cartella denominata "*fabric8*".

1. Creare tre file di frammenti YAML nella nuova cartella *fabric8*:

   a. Creare un file denominato **deployment.yml** con il contenuto seguente:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Creare un file denominato **secrets.yml** con il contenuto seguente:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Creare un file denominato **service.yml** con il contenuto seguente:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Eseguire il comando seguente di Maven per compilare il file dell'elenco di risorse di Kubernetes:

   ```shell
   mvn fabric8:resource
   ```

   Questo comando unisce in tutti i file YAML delle risorse Kubernetes nella cartella *src/main/fabric8* in un unico file YAML che contiene un elenco di risorse Kubernetes, può essere applicato al cluster Kubernetes direttamente o esportato in un grafico di Helm.

   Maven visualizzerà i risultati della compilazione, ad esempio:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Eseguire il comando seguente di Maven per applicare il file dell'elenco di risorse al cluster Kubernetes:

   ```shell
   mvn fabric8:apply
   ```

   Maven visualizzerà i risultati della compilazione, ad esempio:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. Dopo la distribuzione dell'app nel cluster, eseguire una query sull'indirizzo IP esterno tramite l'applicazione `kubectl`, ad esempio:
   ```shell
   kubectl get svc -w
   ```

   In `kubectl` verranno visualizzati gli indirizzi IP interni ed esterni, ad esempio:
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   Per aprire l'applicazione in un Web browser, è possibile usare l'indirizzo IP esterno.

   ![Esplorare l'applicazione di esempio esternamente][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Eliminare il cluster Kubernetes

Quando il cluster Kubernetes non è più necessario, è possibile usare il comando `az group delete` per rimuovere il gruppo di risorse e, di conseguenza, tutte le relative risorse, ad esempio:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso delle applicazioni Spring Boot in Azure, vedere gli articoli seguenti:

* [Distribuire un'applicazione Spring Boot nel servizio app di Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure](container-service-deploy-spring-boot-app-on-linux.md)
* [Distribuire un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per maggiori dettagli sul progetto di esempio di Spring Boot in Docker, vedere [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker).

Per informazioni su come iniziare a creare applicazioni Spring Boot proprie, vedere **Spring Initializr** all'indirizzo <https://start.spring.io/>.

Per altre informazioni su come iniziare a creare una semplice applicazione Spring Boot, vedere Spring Initializr all'indirizzo <https://start.spring.io/>.

Per altri esempi sull'uso delle immagini personalizzate di Docker con Azure, vedere [Uso di un'immagine Docker personalizzata per App Web di Azure in Linux].

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[servizio contenitore di Azure]: https://azure.microsoft.com/services/container-service/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Uso di un'immagine Docker personalizzata per App Web di Azure in Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker (Introduzione a Spring Boot in Docker)
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png
