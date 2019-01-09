---
title: Creare un app Web Java in Linux - Servizio app di Azure
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
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 0c72318f6c80563d138d9c885ea5984a22c5c7fa
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653901"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Guida introduttiva: Creare un'app Java nel servizio app in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida introduttiva spiega come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in Maven per App Web di Azure (anteprima)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file di archivio Web Java (WAR).

![App di esempio in esecuzione in Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creare un'app Java

Eseguire il comando Maven seguente nel prompt di Cloud Shell per creare una nuova app denominata `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Configurare il plug-in Maven

Per eseguire la distribuzione da Maven, utilizzare l'editor di codice nella Cloud Shell per aprire il file `pom.xml` del progetto nella directory `helloworld`. 

```bash
code pom.xml
```

Quindi aggiungere la definizione di plug-in seguente all'interno dell'elemento `<build>` del file `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
      
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
   
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> In questo articolo vengono usate solo app Java in pacchetto con file WAR. Il plug-in supporta anche le applicazioni Web JAR. Vedere [Distribuire un file JAR Java SE nel servizio app di Azure in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per provarlo.


Aggiornare i segnaposto seguenti nella configurazione del plug-in:

| Segnaposto | Descrizione |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Nome del nuovo gruppo di risorse in cui creare l'app. Inserendo tutte le risorse per un'app in un gruppo è possibile gestirle insieme. Ad esempio, eliminando il gruppo di risorse si eliminano tutte le risorse associate all'app. Aggiornare questo valore con un nuovo nome univoco di gruppo di risorse, ad esempio *TestResources*. Questo nome di gruppo di risorse verrà usato per pulire tutte le risorse di Azure in una sezione successiva. |
| `WEBAPP_NAME` | Il nome dell'app sarà incluso nel nome host dell'app durante la distribuzione in Azure (WEBAPP_NAME.azurewebsites.net). Aggiornare questo valore con un nome univoco per la nuova app del servizio app, che ospiterà l'app Java, ad esempio *contoso*. |
| `REGION` | Area di Azure in cui è ospitata l'app, ad esempio `westus2`. È possibile ottenere un elenco di aree da Cloud Shell o dall'interfaccia della riga di comando usando il comando `az account list-locations`. |

## <a name="deploy-the-app"></a>Distribuire l'app

Per distribuire l'app Java in Azure, usare il comando seguente:

```bash
mvn package azure-webapp:deploy
```

Al termine della distribuzione, passare all'applicazione distribuita usando l'URL seguente nel Web browser, ad esempio `http://<webapp>.azurewebsites.net/helloworld`. 

![App di esempio in esecuzione in Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app in Linux è stata completata.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato usato Maven per creare un'app Java, è stato configurato il [plug-in Maven per le app Web di Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) e quindi è stata distribuita un'app Java inclusa in pacchetti come archivio Web nel servizio app in Linux. Fare riferimento alle seguenti esercitazioni e articoli sulle procedure per altre informazioni sull'hosting delle applicazioni Java nel servizio App in Linux.

- [Esercitazione: Distribuire un'app aziendale Java con PostgreSQL](tutorial-java-enterprise-postgresql-app.md)
- [Configurare un'origine dati di Tomcat](app-service-linux-java.md#connecting-to-data-sources)
- [Integrazione continua e distribuzione continua con Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [Configurare gli strumenti di monitoraggio delle prestazioni delle applicazioni](how-to-java-apm-monitoring.md)

