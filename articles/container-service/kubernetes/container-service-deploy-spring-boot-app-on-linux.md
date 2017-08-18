---
title: Distribuire un'app Web Spring Boot su Linux nel servizio contenitore di Azure | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato la procedura per la distribuzione di un'applicazione Spring Boot come app Web Linux in Microsoft Azure.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure

[Spring Framework] è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise. Uno dei progetti più comuni che si basa su Java è [Spring Boot], che offre un approccio semplificato per la creazione di applicazioni Java autonome.

[Docker] è una soluzione open source che consente agli sviluppatori di automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in esecuzione nei contenitori.

Questa esercitazione illustra come usare Docker per sviluppare e distribuire un'applicazione Spring Boot in un host Linux nel [servizio contenitore di Azure].

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura di questa esercitazione, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN] oppure iscriversi per ottenere un [account Azure gratuito].
* [Interfaccia della riga di comando di Azure].
* Un [Java Developer Kit (JDK)] aggiornato.
* Lo strumento di compilazione [Maven] di Apache (versione 3).
* Un client [Git].
* Un client [Docker].

> [!NOTE]
>
> A causa dei requisiti di virtualizzazione dell'esercitazione, non è possibile eseguire i passaggi di questo articolo in una macchina virtuale, ma è necessario usare un computer fisico con le funzionalità di virtualizzazione abilitate.
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Creare l'app Web introduttiva di Spring Boot in Docker

I passaggi seguenti consentono di creare una semplice applicazione Web Spring Boot e di testarla localmente.

1. Aprire un prompt dei comandi. Creare quindi una directory locale in cui salvare l'applicazione, quindi passare a tale directory. Ad esempio:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - o-
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. Clonare il progetto di esempio [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker) nella directory appena creata, ad esempio:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. Passare alla directory del progetto completato. Ad esempio:
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **Passaggio facoltativo**: per eseguire il server Tomcat incorporato sulla porta 80 invece che sulla porta predefinita 8080, ad esempio se si intende testare il progetto Spring Boot in locale, configurare la porta usando la procedura seguente:

   a. Passare alla directory alla directory delle risorse. Ad esempio:
   ```
   cd src/main/resources
   ```

   b. Aprire il file **application.yml** in un editor di testo.

   c. Modificare l'impostazione **server:** in modo che il server venga eseguito sulla porta 80. Ad esempio:
   ```
   server:
      port: 80
   ```

   d. Salvare e chiudere il file **application.yml**.

   e. Cambiare di nuovo directory passando alla cartella radice del progetto completato. Ad esempio:
   ```
   cd ../../..
   ```

5. Compilare il file JAR usando Maven. Ad esempio:
   ```
   mvn package
   ```

6. Dopo avere creato l'app Web, passare alla directory `target` in cui si trova il file JAR e quindi avviare l'app Web. Ad esempio:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. Testare l'app Web accedendovi localmente con un Web browser. È ad esempio possibile usare il comando seguente se curl è disponibile e si è configurato il server Tomcat per l'esecuzione sulla porta 80:
   ```
   curl http://localhost
   ```

8. Dovrebbe essere visualizzato il messaggio seguente: **Hello Docker World!**

   ![Esplorare l'app di esempio in locale][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Creare un registro contenitori di Azure da usare come registro Docker privato

La procedura seguente illustra come usare il portale di Azure per creare un registro contenitori di Azure.

> [!NOTE]
> Per usare l'interfaccia della riga di comando di Azure invece del portale di Azure, seguire i passaggi in [Creare un registro contenitori Docker privato usando l'interfaccia della riga di comando di Azure 2.0][1].

1. Accedere al [portale di Azure].

    Dopo avere effettuato l'accesso all'account nel portale di Azure, seguire i passaggi in [Creare un registro contenitori Docker privato usando il portale di Azure]. I passaggi di tale articolo sono riepilogati qui:

2. Selezionare l'icona del menu **+ Nuovo**.

3. Selezionare **Contenitori** e quindi selezionare **Registro contenitori di Azure**.

   ![Creare un nuovo registro contenitori di Azure][AR01]

4. Quando viene visualizzata la pagina delle informazioni per il modello Registro contenitori di Azure, fare clic su **Crea**.

   ![Creare un nuovo registro contenitori di Azure][AR02]

5. Quando viene visualizzato il pannello **Crea registro contenitori**:

   a. Immettere **Nome registro** e **Gruppo di risorse**.  
   
   b. Selezionare **Abilita** per **Utente amministratore**.
   
   c. Selezionare **Crea**.

   ![Configurare le impostazioni del registro contenitori di Azure][AR03]

6. Dopo che il registro contenitori è stato creato, accedervi nel portale di Azure. Selezionare quindi **Chiavi di accesso**. Prendere nota del nome utente e della password per i passaggi successivi.

   ![Chiavi di accesso al registro contenitori di Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurare Maven per l'uso delle chiavi di accesso del Registro contenitori di Azure

1. Andare alla directory di configurazione dell'installazione di Maven. Aprire quindi il file **settings.xml** con un editor di testo.

2. Aggiungere le impostazioni di accesso al Registro contenitori di Azure dalla sezione precedente di questa esercitazione alla raccolta `<servers>` nel file **settings.xml** file. Ad esempio:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Andare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, **C:\SpringBoot\gs-spring-boot-docker\complete** o **/users/robert/SpringBoot/gs-spring-boot-docker/complete**). Aprire quindi il file **pom.xml** con un editor di testo.

1. Aggiornare la raccolta `<properties>` nel file **pom.xml**. Usare il valore del server di accesso per il registro contenitori di Azure dalla sezione precedente di questa esercitazione. Ad esempio:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Aggiornare la raccolta `<plugins>` nel file **pom.xml** in modo che `<plugin>` contenga l'indirizzo del server di accesso e il nome del registro contenitori di Azure dalla sezione precedente di questa esercitazione, ad esempio:

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

1. Andare alla directory del progetto completato per l'applicazione Spring Boot. Usare quindi il comando seguente per ricompilare l'applicazione ed eseguire il push del contenitore nel registro contenitori di Azure:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Quando si esegue il push del contenitore Docker in Azure, è possibile che venga visualizzato un messaggio di errore simile a uno dei seguenti, anche se il contenitore Docker è stato creato correttamente:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> In questo caso, potrebbe essere necessario accedere ad Azure dalla riga di comando di Docker. Ad esempio:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> È quindi possibile effettuare il push del contenitore dalla riga di comando. Ad esempio:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>Creare un'app Web in Linux nel servizio app di Azure usando l'immagine del contenitore

1. Accedere al [portale di Azure].

1. Fare clic sull'icona del menu **+ Nuovo**, quindi su **Web e dispositivi mobili**.

2.  Fare clic su **App Web in Linux**.

   ![Creare una nuova app Web nel portale di Azure][LX01]

3. Quando viene visualizzato il pannello **App Web in Linux**, seguire questa procedura:

   a. Immettere un nome univoco per **Nome app**, ad esempio *wingtiptoyslinux*.

   b. Scegliere una **Sottoscrizione** dall'elenco a discesa.

   c. Per creare un nuovo gruppo di risorse, scegliere un **Gruppo di risorse** esistente o specificare un nome.

   d. Fare clic su **Configura contenitore** e quindi immettere le informazioni seguenti:

      * Scegliere **Registro di sistema privato**.

      * **Immagine e tag facoltativo**: specificare il nome del contenitore dai passaggi precedenti, ad esempio: *wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*.

      * **URL server**: specificare l'URL del registro dai passaggi precedenti, ad esempio: *https://wingtiptoysregistry.azurecr.io*.

      * **Nome utente di accesso** e **Password**: specificare le credenziali di accesso dalle **Chiavi di accesso** usate nei passaggi precedenti.

   e. Dopo avere immesso tutte le informazioni precedenti, fare clic su **OK**.

   ![Configurare le impostazioni dell'app Web][LX02]

1. Fare clic su **Crea**.

> [!NOTE]
> Azure esegue automaticamente il mapping delle richieste Internet al server Tomcat incorporato in esecuzione sulle porte standard 80 o 8080. Se tuttavia il server Tomcat incorporato è stato configurato per l'esecuzione su una porta personalizzata, è necessario aggiungere all'app Web una variabile di ambiente che definisce la porta del server Tomcat incorporato. A tale scopo, seguire questa procedura:
>
>1. Accedere al [portale di Azure].

>2. Selezionare l'icona per **Servizi app**. Vedere l'elemento 1 nell'immagine seguente.

>3. Selezionare l'app Web dall'elenco. Vedere l'elemento 2 nell'immagine seguente.

>4. Fare clic su **Impostazioni applicazione**. Vedere l'elemento 3 nell'immagine seguente.

>5. Nella sezione **Impostazioni app** aggiungere una nuova variabile di ambiente denominata **PORT**. Immettere quindi il numero di porta personalizzato per il valore. Vedere l'elemento 4 nell'immagine seguente.

>6. Selezionare **Salva**. Vedere l'elemento 5 nell'immagine seguente.

> ![Salvataggio di un numero di porta personalizzato nel portale di Azure][LX03]
>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso delle applicazioni Spring Boot in Azure, vedere gli articoli seguenti:

- [Distribuire un'applicazione Spring Boot nel servizio app di Azure][2]


- [Running a Spring Boot Application on a Kubernetes Cluster in Azure Container Service](container-service-deploy-spring-boot-app-on-kubernetes.md) (Esecuzione di un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per altre informazioni sul progetto di esempio di Spring Boot in Docker, vedere [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker).

Per iniziare a usare le proprie applicazioni Spring Boot, vedere [Spring Initializr](https://start.spring.io/).

Per altre informazioni su come iniziare a creare una semplice applicazione Spring Boot, vedere [Spring Initializr](https://start.spring.io/).

Per altri esempi che illustrano come usare le immagini personalizzate di Docker con Azure, vedere [Uso di un'immagine Docker personalizzata per App Web di Azure in Linux].

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[servizio contenitore di Azure]: https://azure.microsoft.com/services/container-service/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[Creare un registro contenitori Docker privato usando il portale di Azure]: /azure/container-registry/container-registry-get-started-portal
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

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

