---
title: Come usare il plug-in Maven per l'app Web per contenitori per distribuire un'app Spring Boot in contenitore in Azure
description: Informazioni su come usare il plug-in Maven per l'app Web per contenitori per distribuire un'app Spring Boot in Azure.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: 0329aa9b88c7542ab3235a104a0652cd217ff872
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-use-the-maven-plugin-for-web-app-for-containers-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Come usare il plug-in Maven per l'app Web per contenitori per distribuire un'app Spring Boot in contenitore in Azure

Il [plug-in Maven per l'app Web per contenitori](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) per [Apache Maven](http://maven.apache.org/) consente una facile integrazione di Servizio app di Azure nei progetti Maven e semplifica il processo con cui gli sviluppatori distribuiscono app Web in Servizio app di Azure.

Questo articolo illustra l'uso del plug-in Maven per l'app Web per contenitori per distribuire un'applicazione Spring Boot di esempio in un contenitore Docker nell'app Web per contenitori.

> [!NOTE]
>
> Il plug-in Maven per l'app Web per contenitori è attualmente disponibile in anteprima. Per il momento è supportata solo la pubblicazione FTP, ma sono previste in futuro funzionalità aggiuntive.
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

## <a name="clone-the-sample-spring-boot-application"></a>Clonare l'applicazione Spring Boot di esempio

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

1. Clonare il progetto di esempio [Spring Boot con Docker] nella directory creata, ad esempio:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot-docker
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

## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

In questa sezione si crea un'entità servizio di Azure che verrà usata dal plug-in Maven durante la distribuzione del contenitore in Azure.

1. Aprire un prompt dei comandi.

1. Accedere all'account Azure con l'interfaccia della riga di comando di Azure:
   ```shell
   az login
   ```
   Seguire le istruzioni per completare il processo di accesso.

1. Creare un'entità servizio di Azure:
   ```shell
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

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Configurare Maven per l'uso dell'entità servizio di Azure

In questa sezione si usano i valori dell'entità servizio di Azure per configurare l'autenticazione che verrà usata da Maven durante la distribuzione del contenitore in Azure.

1. Aprire il file `settings.xml` di Maven in un editor di testo. Il file potrebbe trovarsi in un percorso come quelli riportati negli esempi seguenti:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Aggiungere le impostazioni dell'entità servizio di Azure della sezione precedente di questa esercitazione alla raccolta `<servers>` nel file *settings.xml*. Ad esempio:

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
   `<environment>` | Definisce l'ambiente cloud di Azure di destinazione, che in questo esempio è `AZURE`. Un elenco completo degli ambienti è disponibile nella documentazione del [plug-in Maven per l'app Web per contenitori].

1. Salvare e chiudere il file *settings.xml*.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>FACOLTATIVO: distribuire il file Docker locale nell'hub Docker

Se si ha un account Docker, è possibile compilare l'immagine del contenitore Docker in locale ed eseguirne il push nell'hub Docker. A tale scopo, seguire questa procedura.

1. Aprire il file `pom.xml` per l'applicazione Spring Boot in un editor di testo.

1. Individuare l'elemento figlio `<imageName>` nell'elemento `<containerSettings>`.

1. Aggiornare il valore `${docker.image.prefix}` con il nome dell'account Docker:
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. Scegliere uno dei metodi di distribuzione seguenti.

   * Compilare l'immagine del contenitore in locale con Maven e quindi usare Docker per eseguire il push del contenitore nell'hub Docker:
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * Se è installato il [plug-in Docker per Maven], è possibile compilare automaticamente l'immagine del contenitore nell'hub Docker usando il parametro `-DpushImage`:
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>FACOLTATIVO: personalizzare pom.xml prima della distribuzione del contenitore in Azure

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
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
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

È possibile modificare diversi valori per il plug-in Maven. Una descrizione dettagliata di ognuno di questi elementi è disponibile nella documentazione del [plug-in Maven per l'app Web per contenitori]. Dopo questa premessa, in questo articolo è opportuno evidenziare diversi valori:

Elemento | Descrizione
---|---|---
`<version>` | Specifica la versione del [plug-in Maven per l'app Web per contenitori]. È consigliabile controllare la versione riportata nel [repository centrale Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) per assicurarsi di usare l'ultima versione.
`<authentication>` | Specifica le informazioni di autenticazione per Azure, che in questo esempio includono un elemento `<serverId>` contenente `azure-auth`. Maven usa questo valore per cercare i valori dell'entità servizio di Azure nel file *settings.xml* di Maven, in base a quanto definito in una sezione precedente di questo articolo.
`<resourceGroup>` | Specifica il gruppo di risorse di destinazione, che in questo esempio è `maven-plugin`. Se non esiste già, questo gruppo di risorse verrà creato durante la distribuzione.
`<appName>` | Specifica il nome di destinazione dell'app Web. In questo esempio, il nome di destinazione è `maven-linux-app-${maven.build.timestamp}` e il suffisso `${maven.build.timestamp}` viene accodato per evitare conflitti. Il timestamp è facoltativo. Come nome dell'app è possibile specificare qualsiasi stringa univoca.
`<region>` | Specifica l'area di destinazione, che in questo esempio è `westus`. Un elenco completo è disponibile nella documentazione del [plug-in Maven per l'app Web per contenitori].
`<appSettings>` | Specifica qualsiasi impostazione univoca che dovrà essere usata da Maven durante la distribuzione dell'app Web in Azure. In questo esempio, un elemento `<property>` contiene una coppia nome-valore di elementi figlio che specificano la porta dell'app.

> [!NOTE]
>
> Le impostazioni per modificare il numero di porta in questo esempio sono necessarie solo in caso di modifica della porta rispetto all'impostazione predefinita.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Compilare e distribuire il contenitore in Azure

Dopo aver configurato tutte le impostazioni nelle sezioni precedenti di questo articolo, è possibile distribuire il contenitore in Azure. A tale scopo, seguire questa procedura:

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
> ```bash
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

Per altre informazioni sulle varie tecnologie illustrate in questo articolo, vedere gli articoli seguenti:

* [plug-in Maven per l'app Web per contenitori]

* [Accedere ad Azure dall'interfaccia della riga di comando di Azure](/azure/xplat-cli-connect)

* [Come usare il plug-in Maven per l'app Web per contenitori per distribuire un'app Spring Boot in Servizio app di Azure in Linux](../app-service-web-deploy-spring-boot-app-with-maven-plugin.md)

* [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Informazioni di riferimento sulle impostazioni di Maven](https://maven.apache.org/settings.html)

* [plug-in Docker per Maven]

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[plug-in Docker per Maven]: https://github.com/spotify/docker-maven-plugin
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[plug-in Maven per l'app Web per contenitori]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP02.png
