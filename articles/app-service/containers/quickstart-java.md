---
title: Guida introduttiva per la creazione di un'app Web Java nel servizio app di Azure in Linux
description: Questa guida introduttiva spiega come distribuire la prima app Java Hello World nel servizio app di Azure in pochi minuti.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355186"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Guida introduttiva: Creare un'app Web Java nel servizio app in Linux

Il servizio app in Linux attualmente offre una funzionalità in anteprima per supportare app Web Java. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida introduttiva spiega come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in Maven per App Web di Azure (anteprima)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un'app Web Java con un'immagine Linux incorporata.

![App di esempio in esecuzione in Azure](media/quickstart-java/java-hello-world-in-browser.png)

La [distribuzione di app Web Java in un contenitore Linux nel cloud con Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) costituisce un approccio alternativo alla distribuzione di un'app Java in un contenitore dell'utente.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva: 

* [Interfaccia della riga di comando di Azure 2.0 o versione successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installata in locale.
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Creare un'app Java

Eseguire il comando seguente usando Maven per creare una nuova app Web *helloworld*:  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Passare alla nuova directory di progetto *helloworld* e compilare tutti i moduli usando il comando seguente:

    mvn verify

Questo comando consentirà di verificare e creare tutti i moduli, incluso il file *helloworld.war* nella sottodirectory *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Distribuzione dell'app Java nel servizio app in Linux

Sono disponibili varie opzioni per distribuire le app Web Java nel servizio app in Linux. Tali opzioni includono:

* [Distribuzione tramite il plug-in Maven per App Web di Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Distribuzione tramite un file ZIP o WAR](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Distribuzione tramite FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

In questa guida introduttiva si userà il plug-in Maven per App Web di Azure. Questo approccio presenta diversi vantaggi perché è facilmente utilizzabile da Maven e crea automaticamente le risorse di Azure necessarie (gruppo di risorse, piano di servizio app e app Web).

### <a name="deploy-with-maven"></a>Distribuire con Maven

Per distribuire da Maven, aggiungere la definizione di plug-in seguente all'interno dell'elemento `<build>` del file *pom.xml*:

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Aggiornare i segnaposto seguenti nella configurazione del plug-in:

| Segnaposto | Descrizione |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Nome del nuovo gruppo di risorse in cui creare l'app Web. Inserendo tutte le risorse per un'app in un gruppo è possibile gestirle insieme. Ad esempio, eliminando il gruppo di risorse si eliminano tutte le risorse associate all'app. Aggiornare questo valore con un nuovo nome univoco di gruppo di risorse, ad esempio *TestResources*. Questo nome di gruppo di risorse verrà usato per pulire tutte le risorse di Azure in una sezione successiva. |
| `YOUR_WEB_APP` | Il nome dell'app sarà incluso nel nome host dell'app Web durante la distribuzione in Azure (YOUR_WEB_APP.azurewebsites.net). Aggiornare questo valore con un nome univoco per l'app Web di Azure, che ospiterà l'app Java, ad esempio *contoso*. |

L'elemento `linuxRuntime` della configurazione consente di controllare in che modo viene usata l'immagine Linux predefinita con l'applicazione.

Eseguire il comando seguente e seguire tutte le istruzioni per l'autenticazione con l'interfaccia della riga di comando di Azure:

    az login

Per distribuire l'app Java nell'app Web, usare il comando seguente:

    mvn clean package azure-webapp:deploy


Al termine della distribuzione, passare all'applicazione distribuita usando l'URL seguente nel Web browser.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

Il codice di esempio Java è in esecuzione in un'app Web con l'immagine predefinita.

![App di esempio in esecuzione in Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app in Linux è stata completata.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato usato Maven per creare un'app Web Java, che è stata quindi distribuita nel servizio app in Linux. Per altre informazioni sull'uso di Java con Azure, visitare il collegamento seguente.

> [!div class="nextstepaction"]
> [Azure per sviluppatori Java](https://docs.microsoft.com/java/azure/)

