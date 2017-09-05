---
title: Distribuire un'applicazione Spring Boot nel servizio app di Azure | Microsoft Docs
description: "Questa esercitazione fornirà agli sviluppatori i passaggi per distribuire l'app Web Introduzione a Spring Boot nel servizio app di Azure."
services: app-service\web
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
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 0c388862d927a1492745832225c686670c071f86
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Distribuire un'applicazione Spring Boot nel servizio app di Azure

**[Spring Framework]** è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise; uno dei progetti più comuni che si basa su questa piattaforma è [Spring Boot], che fornisce un approccio semplificato per la creazione di applicazioni Java autonome.

In questa esercitazione verrà illustrata tuttavia la creazione di una app Web Introduzione a Spring Boot Guida di esempio e la sua distribuzione nel [servizio app di Azure].

### <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi di questa esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure; se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN] oppure iscriversi per ottenere un [account Azure gratuito].
* Un [Java Developer Kit (JDK)] aggiornato.
* Lo strumento di compilazione [Maven] di Apache (versione 3).
* Un client [Git].

## <a name="create-the-spring-boot-getting-started-web-app"></a>Creare l'app Web Introduzione a Spring Boot

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

1. Clonare il progetto di esempio [Introduzione a Spring Boot ] nella directory appena creata, ad esempio:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. Passare alla directory del progetto completato. Ad esempio:
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Compilare il file JAR usando Maven. Ad esempio:
   ```
   mvn package
   ```

1. Dopo aver creato l'app Web, passare alla directory del file JAR e avviare l'app Web. Ad esempio:
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. Testare l'app Web passando a http://localhost:8080 tramite un Web browser o usare una sintassi simile all'esempio seguente, se si hanno curl disponibili:
   ```
   curl http://localhost:8080
   ```

1. Si dovrebbe visualizzare il messaggio seguente: **Greetings from Spring Boot!** (Benvenuti in Spring Boot!)

   ![Esplora la app di esempio][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Creare un'app web di Azure con Java

I passaggi seguenti illustrano la creazione di un'app Web di Azure, la configurazione delle impostazioni necessarie per Java e delle proprie credenziali FTP.

1. Aprire il [portale di Azure] ed effettuare l'accesso.

1. Dopo aver effettuato l'accesso all'account nel portale di Azure, fare clic sull'icona di menu per i **servizi App**:
   
   ![Portale di Azure][AZ01]

1. Quando la pagina **servizi App** viene visualizzata, fare clic su **+ Aggiungi** per creare un nuovo servizio App.

   ![Creare un servizio app][AZ02]

1. Quando viene visualizzato l'elenco di modelli di app Web, fare clic sul collegamento per l'applicazione Web Microsoft base.

   ![Modelli di app Web][AZ03]

1. Quando la pagina delle informazioni per il modello di app Web viene visualizzata, fare clic su **Crea**.

   ![Crea app Web][AZ04]

1. Specificare un nome univoco per l'app Web, quindi specificare eventuali impostazioni aggiuntive e scegliere **Crea**.

   ![Creare delle impostazioni app Web][AZ05]

1. Dopo aver creato un'app Web, fare clic sull'icona di menu **servizi App**, quindi fare clic sulla app Web appena creata:

   ![Elenco delle app Web][AZ06]

1. Quando viene visualizzata l'app web, specificare la versione di Java usando la procedura seguente:

   a. Fare clic sulla voce di menu **Impostazioni applicazione**.

   b. Scegliere **Java 8** per la versione di Java.

   c. Scegliere **Più recenti** per la versione di Java.

   d. Scegliere **Newest Tomcat 8.5** [Tomcat 8.5 più recente] per il contenitore Web. (Questo contenitore non verrà effettivamente usato; Azure userà il contenitore dall'app Spring Boot.)

   e. Fare clic su **Salva**.

   ![Impostazioni dell'applicazione][AZ07]

1. Specificare le credenziali di distribuzione FTP usando la procedura seguente:

   a. Fare clic sulla voce di menu **Credenziali distribuzione**.

   b. Specificare il proprio nome utente e la password.

   c. Fare clic su **Salva**.

   ![Specificare le credenziali di distribuzione][AZ08]

1. Recuperare le informazioni di connessione FTP usando la procedura seguente:

   a. Fare clic sulla voce di menu **Credenziali distribuzione**.

   b. Copiare il nome utente FTP completo e l'URL e salvarli per la sezione successiva di questa esercitazione.

   ![URL FTP e credenziali][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>Distribuire l'applicazione Web Spring Boot in Azure

La procedura seguente illustra i passaggi per distribuire l'app Web Spring Boot in Azure.

1. Aprire un editor di testo come Blocco note e incollare il testo seguente in un nuovo documento, quindi salvare il file come *web.config*:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. Dopo aver salvato il file *web.config* nel sistema, effettuare la connessione all'app Web tramite FTP usando l'URL, il nome utente e la password della sezione precedente di questa esercitazione. ad esempio:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. Modificare la directory remota nella cartella radice dell'app Web (ovvero */sito/wwwroot*), quindi copiare il file JAR dall'applicazione Spring Boot e il file *web.config* salvato in precedenza. ad esempio:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. Dopo aver distribuito i file JAR e *web.config* nell'app Web, è necessario riavviare l'app Web tramite il portale di Azure:

   ![][AZ10]

1. Testare l'app Web passando all'URL relativo tramite un browser Web o usare una sintassi simile all'esempio seguente, se si hanno curl disponibili:
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. Si dovrebbe visualizzare il messaggio seguente: **Greetings from Spring Boot!** (Benvenuti in Spring Boot!)

   ![Esplora la app di esempio][SB02]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso delle applicazioni Spring Boot in Azure, vedere gli articoli seguenti:

* [Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [Distribuire un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per altre informazioni sulla distribuzione di app Web in Azure tramite FTP, vedere [Distribuire l'app nel servizio app di Azure usando FTP/S].

Per altre informazioni sul progetto di esempio Spring Boot, vedere [Introduzione a Spring Boot ].

Per informazioni sulla Guida introduttiva con le proprie applicazioni Spring Boot, vedere **Spring Initializr** (Inizializzazione di SpringBoot) all'indirizzo https://start.spring.io/.

Per altre informazioni sulla configurazione delle impostazioni aggiuntive per l'app Web, vedere [Configurare app Web in Servizio app di Azure].

<!-- URL List -->

[servizio app di Azure]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[portale di Azure]: https://portal.azure.com/
[Configurare app Web in Servizio app di Azure]: /azure/app-service-web/web-sites-configure
[Distribuire l'app nel servizio app di Azure usando FTP/S]: https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Introduzione a Spring Boot ]: https://github.com/spring-guides/gs-spring-boot
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png

