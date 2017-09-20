---
title: Come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot del registro contenitori di Azure nel servizio app di Azure
description: Questa esercitazione illustra in modo dettagliato la procedura per distribuire un'applicazione Spring Boot del registro contenitori di Azure nel servizio app di Azure usando un plug-in Maven.
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: f47ee59d72ea49d62be2cb435ebaf8bc841e4198
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot del registro contenitori di Azure nel servizio app di Azure

**[Spring Framework]** è uno dei framework open source più diffusi e consente agli sviluppatori Java di creare applicazioni Web, per dispositivi mobili e per le API. Questa esercitazione usa un'app di esempio creata con [Spring Boot], un approccio basato su convenzioni per l'uso di Spring per iniziare rapidamente a creare app.

Questo articolo illustra come distribuire un'applicazione Spring Boot di esempio nel registro contenitori di Azure e quindi usare il plug-in Maven per App Web di Azure per distribuire l'applicazione nel servizio app di Azure.

> [!NOTE]
>
> Il plug-in Maven per App Web di Azure è attualmente disponibile in anteprima. Per il momento è supportata solo la pubblicazione FTP, ma sono previste in futuro funzionalità aggiuntive.
>

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura di questa esercitazione, sono necessari i prerequisiti seguenti:

* Sottoscrizione di Azure; se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN] oppure iscriversi per ottenere un [account Azure gratuito].
* [Interfaccia della riga di comando di Azure].
* Java Development Kit (JDK) aggiornato, versione 1.7 o successiva.
* Lo strumento di compilazione [Maven] di Apache (versione 3).
* Un client [Git].
* Un client [Docker].

> [!NOTE]
>
> A causa dei requisiti di virtualizzazione di questa esercitazione, non è possibile seguire la procedura illustrata in questo articolo in una macchina virtuale. È necessario usare un computer fisico in cui sono abilitate le funzionalità di virtualizzazione.
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Clonare l'app Web Spring Boot in Docker di esempio

In questa sezione si clona e si testa in locale un'applicazione Spring Boot in contenitore.

1. Aprire un prompt dei comandi o una finestra del terminale e creare una directory locale in cui salvare l'applicazione Spring Boot, quindi passare a tale directory. Ad esempio:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - o-
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clonare il progetto di esempio [Spring Boot on Docker Getting Started] (Introduzione a Spring Boot in Docker) nella directory appena creata, ad esempio:
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
   ```

1. Passare alla directory del progetto completato. Ad esempio:
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Compilare il file JAR usando Maven. Ad esempio:
   ```shell
   mvn clean package
   ```

1. Dopo che è stata creata, avviare l'app Web con Maven, ad esempio:
   ```shell
   mvn spring-boot:run
   ```

1. Testare l'app Web esplorandola localmente tramite un Web browser. Se è disponibile curl, ad esempio, è possibile usare il comando seguente:
   ```shell
   curl http://localhost:8080
   ```

1. Dovrebbe essere visualizzato il messaggio seguente: **Hello Docker World**

   ![Esplorare l'app di esempio in locale][SB01]

## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

In questa sezione si crea un'entità servizio di Azure che verrà usata dal plug-in Maven durante la distribuzione del contenitore in Azure.

1. Aprire un prompt dei comandi.

1. Accedere all'account Azure con l'interfaccia della riga di comando di Azure:
   ```azurecli
   az login
   ```
   Seguire le istruzioni per completare il processo di accesso.

1. Creare un'entità servizio di Azure:
   ```azurecli
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   `uuuuuuuu` è il nome utente e `pppppppp` è la password dell'entità servizio.

1. Azure restituisce una risposta JSON simile all'esempio seguente:
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > I valori di questa risposta JSON verranno usati durante la configurazione del plug-in Maven per la distribuzione del contenitore in Azure. `aaaaaaaa`, `uuuuuuuu`, `pppppppp` e `tttttttt` sono segnaposto che vengono usati in questo esempio per facilitare il mapping di questi valori ai rispettivi elementi durante la configurazione del file `settings.xml` di Maven nella sezione successiva.
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Creare un registro contenitori di Azure usando l'interfaccia della riga di comando di Azure

1. Aprire un prompt dei comandi.

1. Accedere all'account di Azure:
   ```azurecli
   az login
   ```

1. Creare un gruppo di risorse per le risorse di Azure che verranno usate in questo articolo:
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Sostituire il valore `wingtiptoysresources` di questo esempio con un nome univoco per il gruppo di risorse.

1. Creare un registro contenitori privato di Azure nel gruppo di risorse per l'app Spring Boot: 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Sostituire il valore `wingtiptoysregistry` di questo esempio con un nome univoco per il registro contenitori.

1. Recuperare la password per il registro contenitori:
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure restituirà la password, ad esempio:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Aggiungere il registro contenitori e l'entità servizio di Azure alle impostazioni di Maven

1. Aprire il file `settings.xml` di Maven in un editor di testo. Il file potrebbe trovarsi in un percorso come quelli riportati negli esempi seguenti:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Aggiungere le impostazioni di accesso per il registro contenitori di Azure della sezione precedente di questo articolo alla raccolta `<servers>` nel file *settings.xml*. Ad esempio:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Dove:
   Elemento | Descrizione
   ---|---|---
   `<id>` | Contiene il nome del registro contenitori privato di Azure.
   `<username>` | Contiene il nome del registro contenitori privato di Azure.
   `<password>` | Contiene la password recuperata nella sezione precedente di questo articolo.

1. Aggiungere le impostazioni dell'entità servizio di Azure di una sezione precedente di questo articolo alla raccolta `<servers>` nel file *settings.xml*. Ad esempio:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Dove:
   Elemento | Descrizione
   ---|---|---
   `<id>` | Specifica un nome univoco che viene usato da Maven per cercare le impostazioni di sicurezza quando si distribuisce l'app Web in Azure.
   `<client>` | Contiene il valore `appId` dell'entità servizio.
   `<tenant>` | Contiene il valore `tenant` dell'entità servizio.
   `<key>` | Contiene il valore `password` dell'entità servizio.
   `<environment>` | Definisce l'ambiente cloud di Azure di destinazione, che in questo esempio è `AZURE`. Un elenco completo degli ambienti è disponibile nella documentazione del [plug-in Maven per App Web di Azure].

1. Salvare e chiudere il file *settings.xml*.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Compilare l'immagine del contenitore Docker ed eseguirne il push nel registro contenitori di Azure

1. Passare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, "*C:\SpringBoot\gs-spring-boot-docker\complete*" o "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") e aprire il file *pom.xml* con un editor di testo.

1. Aggiornare la raccolta `<properties>` nel file *pom.xml* con il valore del server di accesso per il Registro contenitori di Azure dalla sezione precedente di questa esercitazione. Ad esempio:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Dove:
   Elemento | Descrizione
   ---|---|---
   `<azure.containerRegistry>` | Specifica il nome del registro contenitori privato di Azure.
   `<docker.image.prefix>` | Specifica l'URL del registro contenitori privato di Azure, ottenuto accodando ".azurecr.io" al nome del registro contenitori privato.

1. Verificare che l'elemento `<plugin>` per il plug-in Docker nel file *pom.xml* contenga le proprietà corrette per l'indirizzo del server di accesso e il nome del registro dal passaggio precedente di questa esercitazione. Ad esempio:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Dove:
   Elemento | Descrizione
   ---|---|---
   `<serverId>` | Specifica la proprietà contenente il nome del registro contenitori privato di Azure.
   `<registryUrl>` | Specifica la proprietà contenente l'URL del registro contenitori privato di Azure.

1. Passare alla directory del progetto completato per l'applicazione Spring Boot ed eseguire questo comando per ricompilare l'applicazione ed effettuare il push del contenitore nel registro contenitori di Azure:

   ```
   mvn package docker:build -DpushImage 
   ```

1. FACOLTATIVO: passare al [portale di Azure] e verificare che nel registro contenitori sia presente l'immagine del contenitore Docker denominata **gs-spring-boot-docker**.

   ![Verificare il contenitore nel portale di Azure][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Personalizzare pom.xml e quindi compilare e distribuire il contenitore in Azure

Aprire il file `pom.xml` per l'applicazione Spring Boot in un editor di testo e quindi individuare l'elemento `<plugin>` per `azure-webapp-maven-plugin`. L'elemento dovrebbe essere simile all'esempio seguente:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

È possibile modificare diversi valori per il plug-in Maven. Una descrizione dettagliata di ognuno di questi elementi è disponibile nella documentazione del [plug-in Maven per App Web di Azure]. Dopo questa premessa, in questo articolo è opportuno evidenziare diversi valori:

Elemento | Descrizione
---|---|---
`<version>` | Specifica la versione del [plug-in Maven per App Web di Azure]. È consigliabile controllare la versione riportata nel [repository centrale Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) per assicurarsi di usare l'ultima versione.
`<authentication>` | Specifica le informazioni di autenticazione per Azure, che in questo esempio includono un elemento `<serverId>` contenente `azure-auth`. Maven usa questo valore per cercare i valori dell'entità servizio di Azure nel file *settings.xml* di Maven, in base a quanto definito in una sezione precedente di questo articolo.
`<resourceGroup>` | Specifica il gruppo di risorse di destinazione, che in questo esempio è `wingtiptoysresources`. Se non esiste già, questo gruppo di risorse verrà creato durante la distribuzione.
`<appName>` | Specifica il nome di destinazione dell'app Web. In questo esempio, il nome di destinazione è `maven-linux-app-${maven.build.timestamp}` e il suffisso `${maven.build.timestamp}` viene accodato per evitare conflitti. Il timestamp è facoltativo. Come nome dell'app è possibile specificare qualsiasi stringa univoca.
`<region>` | Specifica l'area di destinazione, che in questo esempio è `westus`. Un elenco completo è disponibile nella documentazione del [plug-in Maven per App Web di Azure].
`<containerSettings>` | Specifica le proprietà contenenti il nome e l'URL del contenitore.
`<appSettings>` | Specifica qualsiasi impostazione univoca che dovrà essere usata da Maven durante la distribuzione dell'app Web in Azure. In questo esempio, un elemento `<property>` contiene una coppia nome-valore di elementi figlio che specificano la porta dell'app.

> [!NOTE]
>
> Le impostazioni per modificare il numero di porta in questo esempio sono necessarie solo in caso di modifica della porta rispetto all'impostazione predefinita.
>

1. Se sono state apportate modifiche al file *pom.xml*, dalla finestra del terminale o al prompt dei comandi usato in precedenza ricompilare il file JAR usando Maven. Ad esempio:
   ```shell
   mvn clean package
   ```

1. Distribuire l'app Web in Azure usando Maven. Ad esempio:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven distribuirà l'app Web in Azure. Se l'app Web non esiste già, verrà creata.

> [!NOTE]
>
> Se nell'area specificata nell'elemento `<region>` del file *pom.xml* non sono disponibili server sufficienti quando si avvia la distribuzione, potrebbe essere visualizzato un errore simile all'esempio seguente:
>
> ```
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> In questo caso, è possibile specificare un'altra area e rieseguire il comando Maven per distribuire l'applicazione.
>
>

Dopo che è stata distribuita, l'app Web potrà essere gestita usando il [portale di Azure].

* L'app Web sarà elencata in **Servizi app**:

   ![App Web elencata in Servizi app nel portale di Azure][AP01]

* L'URL dell'app Web sarà riportato nella **panoramica** dell'app Web:

   ![Individuazione dell'URL dell'app Web][AP02]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle varie tecnologie illustrate in questo articolo, vedere gli articoli seguenti:

* [plug-in Maven per App Web di Azure]

* [Accedere ad Azure dall'interfaccia della riga di comando di Azure](/azure/xplat-cli-connect)

* [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Informazioni di riferimento sulle impostazioni di Maven](https://maven.apache.org/settings.html)

* [Plug-in Docker per Maven]

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[plug-in Maven per App Web di Azure]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Plug-in Docker per Maven]: https://github.com/spotify/docker-maven-plugin
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker (Introduzione a Spring Boot in Docker)
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png

