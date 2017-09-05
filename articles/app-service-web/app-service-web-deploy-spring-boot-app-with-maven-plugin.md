---
title: Come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot in Azure
description: Informazioni su come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot in Azure.
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
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: dceb7edf788bd87b1de04aa435a12cd5853755b9
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-to-azure"></a>Come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot in Azure

Il [plug-in Maven per App Web di Azure](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) disponibile per [Apache Maven](http://maven.apache.org/) consente una facile integrazione del servizio app di Azure nei progetti Maven e semplifica il processo con cui gli sviluppatori distribuiscono app Web nel servizio app di Azure.

Questo articolo illustra l'uso del plug-in Maven per App Web di Azure per distribuire un'applicazione Spring Boot di esempio in Servizi app di Azure.

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

## <a name="clone-the-sample-spring-boot-web-app"></a>Clonare l'app Web Spring Boot di esempio

In questa sezione si clona e si testa in locale un'applicazione Spring Boot completata.

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

1. Clonare il progetto di esempio di [introduzione a Spring Boot] nella directory creata, ad esempio:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot
   ```

1. Passare alla directory del progetto completato. Ad esempio:
   ```shell
   cd gs-spring-boot/complete
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

1. Si dovrebbe visualizzare il messaggio seguente: **Greetings from Spring Boot!** (Benvenuti in Spring Boot!)

## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

In questa sezione si crea un'entità servizio di Azure che verrà usata dal plug-in Maven durante la distribuzione dell'app Web in Azure.

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
   > I valori di questa risposta JSON verranno usati durante la configurazione del plug-in Maven per la distribuzione dell'app Web in Azure. `aaaaaaaa`, `uuuuuuuu`, `pppppppp` e `tttttttt` sono segnaposto che vengono usati in questo esempio per facilitare il mapping di questi valori ai rispettivi elementi durante la configurazione del file `settings.xml` di Maven nella sezione successiva.
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Configurare Maven per l'uso dell'entità servizio di Azure

In questa sezione si usano i valori dell'entità servizio di Azure per configurare l'autenticazione che verrà usata da Maven durante la distribuzione dell'app Web in Azure.

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
   `<environment>` | Definisce l'ambiente cloud di Azure di destinazione, che in questo esempio è `AZURE`. Un elenco completo degli ambienti è disponibile nella documentazione del [plug-in Maven per App Web di Azure].

1. Salvare e chiudere il file *settings.xml*.

## <a name="optional-customize-your-pomxml-before-deploying-your-web-app-to-azure"></a>FACOLTATIVO: personalizzare pom.xml prima della distribuzione dell'app Web in Azure

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
         <appName>maven-web-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <javaVersion>1.8</javaVersion>
         <deploymentType>ftp</deploymentType>
         <resources>
            <resource>
               <directory>${project.basedir}/target</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>*.jar</include>
               </includes>
            </resource>
            <resource>
               <directory>${project.basedir}</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>web.config</include>
               </includes>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```

È possibile modificare diversi valori per il plug-in Maven. Una descrizione dettagliata di ognuno di questi elementi è disponibile nella documentazione del [plug-in Maven per App Web di Azure]. Dopo questa premessa, in questo articolo è opportuno evidenziare diversi valori:

Elemento | Descrizione
---|---|---
`<version>` | Specifica la versione del [plug-in Maven per App Web di Azure]. È consigliabile controllare la versione riportata nel [repository centrale Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) per assicurarsi di usare l'ultima versione.
`<authentication>` | Specifica le informazioni di autenticazione per Azure, che in questo esempio includono un elemento `<serverId>` contenente `azure-auth`. Maven usa questo valore per cercare i valori dell'entità servizio di Azure nel file *settings.xml* di Maven, in base a quanto definito in una sezione precedente di questo articolo.
`<resourceGroup>` | Specifica il gruppo di risorse di destinazione, che in questo esempio è `maven-plugin`. Se non esiste già, questo gruppo di risorse viene creato durante la distribuzione.
`<appName>` | Specifica il nome di destinazione dell'app Web. In questo esempio, il nome di destinazione è `maven-web-app-${maven.build.timestamp}` e il suffisso `${maven.build.timestamp}` viene accodato per evitare conflitti. Il timestamp è facoltativo. Come nome dell'app è possibile specificare qualsiasi stringa univoca.
`<region>` | Specifica l'area di destinazione, che in questo esempio è `westus`. Un elenco completo è disponibile nella documentazione del [plug-in Maven per App Web di Azure].
`<javaVersion>` | Specifica la versione del runtime Java per l'app Web. Un elenco completo è disponibile nella documentazione del [plug-in Maven per App Web di Azure].
`<deploymentType>` | Specifica il tipo di distribuzione per l'app Web. Per il momento è supportato solo `ftp`, ma è in fase di sviluppo il supporto di altri tipi di distribuzione.
`<resources>` | Specifica le risorse e le destinazioni che verranno usate da Maven durante la distribuzione dell'app Web in Azure. In questo esempio, due elementi `<resource>` specificano che Maven distribuirà il file JAR per l'app Web e il file *web.config* del progetto Spring Boot.

## <a name="build-and-deploy-your-web-app-to-azure"></a>Compilare e distribuire l'app Web in Azure

Dopo aver configurato tutte le impostazioni nelle sezioni precedenti di questo articolo, è possibile distribuire l'app Web in Azure. A tale scopo, seguire questa procedura:

1. Se sono state apportate modifiche al file *pom.xml*, dalla finestra del terminale o al prompt dei comandi usato in precedenza ricompilare il file JAR usando Maven. Ad esempio:
   ```shell
   mvn clean package
   ```

1. Distribuire l'app Web in Azure usando Maven. Ad esempio:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven distribuirà l'app Web in Azure. Se l'app Web non esiste già, verrà creata.

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

* [plug-in Maven per App Web di Azure]

* [Accedere ad Azure dall'interfaccia della riga di comando di Azure](/azure/xplat-cli-connect)

* [Come usare il plug-in Maven per App Web di Azure per distribuire un'app Spring Boot in contenitore in Azure](app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin.md)

* [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Informazioni di riferimento sulle impostazioni di Maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[introduzione a Spring Boot]: https://github.com/microsoft/gs-spring-boot
[Spring Framework]: https://spring.io/
[plug-in Maven per App Web di Azure]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP02.png

