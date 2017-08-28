---
title: Distribuire un'app Web Spring Boot su Linux nel servizio contenitore di Azure | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato la procedura per la distribuzione di un'applicazione Spring Boot come app Web Linux in Microsoft Azure.
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
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 5f0b470bd46cfeaf00b3092dbe9db507ed50f622
ms.contentlocale: it-it
ms.lasthandoff: 08/12/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure

**[Spring Framework]** è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise. Uno dei progetti più comuni che si basa su questa piattaforma è [Spring Boot], che fornisce un approccio semplificato per la creazione di applicazioni Java autonome.

**[Docker]** è una soluzione open source che consente agli sviluppatori di automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in esecuzione nei contenitori.

Questa esercitazione illustra l'uso di Docker per sviluppare e distribuire un'applicazione Spring Boot a un host Linux nel [servizio contenitore di Azure].

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

I passaggi seguenti illustrano i passaggi necessari per creare una semplice applicazione Web Spring Boot e testarla localmente.

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

1. Clonare il progetto di esempio [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker) nella directory appena creata, ad esempio:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Passare alla directory del progetto completato. Ad esempio:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Compilare il file JAR usando Maven. Ad esempio:
   ```
   mvn package
   ```

1. Dopo aver creato l'app Web, passare alla directory `target` in cui si trova il file JAR e avviare l'app Web. Ad esempio:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. Testare l'app Web esplorandola localmente tramite un Web browser. Ad esempio, se è disponibile un cURL e il server Tomcat è configurato per l'esecuzione sulla porta 80:
   ```
   curl http://localhost
   ```

1. Dovrebbe essere visualizzato il messaggio seguente: **Hello Docker World!**

   ![Esplorare l'app di esempio in locale][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Creare un Registro contenitori di Azure da usare come registro Docker privato

La procedura seguente illustra come usare il portale di Azure per creare un Registro contenitori di Azure.

> [!NOTE]
>
> Se si vuole usare l'interfaccia della riga di comando di Azure anziché il portale di Azure, seguire i passaggi in [Creare un registro per contenitori Docker privati usando l'interfaccia della riga di comando di Azure 2.0](../../container-registry/container-registry-get-started-azure-cli.md).
>

1. Aprire il [portale di Azure] ed effettuare l'accesso.

   Dopo aver effettuato l'accesso all'account nel portale di Azure, è possibile seguire la procedura illustrata nell'articolo [Creare un registro per contenitori Docker privati con il portale di Azure], parafrasata per semplicità nei passaggi seguenti.

1. Fare clic sull'icona di menu **+ Nuovo**, su **Contenitori** e quindi su **Registro contenitori di Azure**.
   
   ![Creare un nuovo Registro contenitori di Azure][AR01]

1. Quando viene visualizzata la pagina delle informazioni per il modello di Registro contenitori di Azure, fare clic su **Crea**. 

   ![Creare un nuovo Registro contenitori di Azure][AR02]

1. Quando viene visualizzata la pagina **Crea registro contenitori**, immettere **Nome registro** e **Gruppo di risorse**, scegliere **Abilita** per **Utente amministratore** e quindi fare clic su **Crea**.

   ![Configurare le impostazioni del Registro contenitori di Azure][AR03]

1. Una volta creato il registro contenitori, passare al registro contenitori stesso nel portale di Azure e quindi fare clic su **Chiavi di accesso**. Prendere nota del nome utente e della password per i passaggi successivi.

   ![Chiavi di accesso al Registro contenitori di Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurare Maven per l'uso delle chiavi di accesso del Registro contenitori di Azure

1. Passare alla directory di configurazione dell'installazione di Maven e aprire il file *settings.xml* con un editor di testo.

1. Aggiungere le impostazioni di accesso al Registro contenitori di Azure dalla sezione precedente di questa esercitazione alla raccolta `<servers>` nel file *settings.xml* file. Ad esempio:

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

1. Aggiornare la raccolta `<properties>` nel file *pom.xml* con il valore del server di accesso per il Registro contenitori di Azure dalla sezione precedente di questa esercitazione. Ad esempio:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Aggiornare la raccolta `<plugins>` nel file *pom.xml* in modo che `<plugin>` contenga l'indirizzo del server di accesso e il nome del registro per il Registro contenitori di Azure dalla sezione precedente di questa esercitazione. ad esempio:

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

1. Passare alla directory del progetto completato per l'applicazione Spring Boot ed eseguire il comando seguente per ricompilare l'applicazione ed effettuare il push del contenitore nel Registro contenitori di Azure:

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> Quando si effettua il push del contenitore Docker in Azure, è possibile che venga visualizzato un messaggio di errore simile a uno dei seguenti, anche se il contenitore Docker è stato creato correttamente:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> In questo caso, è possibile che sia necessario accedere all'account Azure dalla riga di comando di Docker. Ad esempio:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> È quindi possibile effettuare il push del contenitore dalla riga di comando. Ad esempio:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Creare un'app Web in Linux nel servizio app di Azure usando l'immagine del contenitore

1. Aprire il [portale di Azure] ed effettuare l'accesso.

1. Fare clic sull'icona di menu **+ Nuovo**, su **Web e dispositivi mobili**, quindi su **App Web in Linux**.
   
   ![Creare una nuova app Web nel portale di Azure][LX01]

1. Quando viene visualizzata la pagina **App Web in Linux**, immettere le informazioni seguenti:

   a. Immettere un nome univoco per il **Nome app**, ad esempio: "*wingtiptoyslinux*".

   b. Scegliere una **Sottoscrizione** dall'elenco a discesa.

   c. Selezionare un **Gruppo di risorse** esistente o specificare un nome per creare uno nuovo.

   d. Fare clic su **Configura contenitore** e immettere le informazioni seguenti:

      * Scegliere **Registro di sistema privato**.

      * **Immagine e tag facoltativo**: specificare il nome del contenitore dai passaggi precedenti, ad esempio: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*"

      * **URL server**: specificare l'URL del registro dai passaggi precedenti, ad esempio: "*https://wingtiptoysregistry.azurecr.io*"

      * **Nome utente di accesso** e **Password**: specificare le credenziali di accesso dalle **Chiavi di accesso** usate nei passaggi precedenti.
   
   e. Dopo aver immesso tutte le informazioni precedenti, fare clic su **OK**.

   ![Configurare le impostazioni dell'app Web][LX02]

1. Fare clic su **Crea**.

> [!NOTE]
>
> Azure eseguirà automaticamente il mapping delle richieste Internet al server Tomcat incorporato in esecuzione sulla porta standard 80 o 8080. Se tuttavia il server Tomcat incorporato è stato configurato per l'esecuzione su una porta personalizzata, è necessario aggiungere all'app Web una variabile di ambiente che definisce la porta del server Tomcat incorporato. A tale scopo, seguire questa procedura:
>
> 1. Aprire il [portale di Azure] ed effettuare l'accesso.
> 
> 2. Fare clic sull'icona per **Servizi app**. (Vedere l'elemento 1 nell'immagine seguente.)
>
> 3. Selezionare l'app Web dall'elenco. (Elemento 2 nell'immagine seguente.)
>
> 4. Fare clic su **Impostazioni applicazione**. (Elemento 3 nell'immagine seguente.)
>
> 5. Nella sezione **Impostazioni app** aggiungere una nuova variabile di ambiente denominata **PORT** e immettere il numero di porta personalizzato come valore. (Elemento 4 nell'immagine seguente.)
>
> 6. Fare clic su **Salva**. (Elemento 5 nell'immagine seguente.)
>
> ![Salvataggio di un numero di porta personalizzato nel portale di Azure][LX03]
>

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso delle applicazioni Spring Boot in Azure, vedere gli articoli seguenti:

* [Distribuire un'applicazione Spring Boot nel servizio app di Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuire un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per maggiori dettagli sul progetto di esempio di Spring Boot in Docker, vedere [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker).

Per informazioni sulla Guida introduttiva con le proprie applicazioni Spring Boot, vedere **Spring Initializr** (Inizializzazione di SpringBoot) all'indirizzo https://start.spring.io/.

Per altre informazioni sul come iniziare a creare una semplice applicazione Spring Boot, vedere Spring Initializr all'indirizzo https://start.spring.io/.

Per altri esempi sull'uso delle immagini personalizzate di Docker con Azure, vedere [Uso di un'immagine Docker personalizzata per App Web di Azure in Linux].

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[servizio contenitore di Azure]: https://azure.microsoft.com/services/container-service/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[Creare un registro per contenitori Docker privati con il portale di Azure]: /azure/container-registry/container-registry-get-started-portal
[Uso di un'immagine Docker personalizzata per App Web di Azure in Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker (Introduzione a Spring Boot in Docker)
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

