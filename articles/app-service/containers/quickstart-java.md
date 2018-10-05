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
ms.openlocfilehash: b9e8d2b9eacfa5c427ffe3f27ea99bbd35651d57
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165977"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Guida introduttiva: Creare un'app Web Java nel servizio app in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida introduttiva spiega come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in Maven per App Web di Azure (anteprima)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file di archivio web Java per applicazioni web (WAR).

![App di esempio in esecuzione in Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creare un'app Java

Eseguire il comando Maven seguente nel prompt di Cloud Shell per creare una nuova app web denominata `helloworld`:

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
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> In questo articolo vengono usate solo app Java in pacchetto con file WAR. Il plug-in supporta anche le applicazioni web con estensione JAR. Utilizzare la seguente definizione di plugin alternativa per queste applicazioni. Questa configurazione distribuirà un JAR compilato da Maven in `${project.build.directory}/${project.build.finalName}.jar` sul filesystem locale.
>
>```xml
><plugin>
>            <groupId>com.microsoft.azure</groupId>
>            <artifactId>azure-webapp-maven-plugin</artifactId>
>            <version>1.4.0</version>
>            <configuration>
>                <deploymentType>jar</deploymentType>
>
>           <!-- Web App information -->
>            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
>            <appName>${WEBAPP_NAME}</appName>
>            <region>${REGION}</region>  
>
>                <!-- Java Runtime Stack for Web App on Linux-->
>                <linuxRuntime>jre8</linuxRuntime>
>            </configuration>
>         </plugin>
>```    


Aggiornare i segnaposto seguenti nella configurazione del plug-in:

| Placeholder | DESCRIZIONE |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Nome del nuovo gruppo di risorse in cui creare l'app Web. Inserendo tutte le risorse per un'app in un gruppo è possibile gestirle insieme. Ad esempio, eliminando il gruppo di risorse si eliminano tutte le risorse associate all'app. Aggiornare questo valore con un nuovo nome univoco di gruppo di risorse, ad esempio *TestResources*. Questo nome di gruppo di risorse verrà usato per pulire tutte le risorse di Azure in una sezione successiva. |
| `WEBAPP_NAME` | Il nome dell'app sarà incluso nel nome host dell'app Web durante la distribuzione in Azure (WEBAPP_NAME.azurewebsites.net). Aggiornare questo valore con un nome univoco per l'app Web di Azure, che ospiterà l'app Java, ad esempio *contoso*. |
| `REGION` | Un'area di Azure in cui l'app web è ospitata, ad esempio `westus2`. È possibile ottenere un elenco di aree da Cloud Shell o dall’interfaccia della riga di comando utilizzando il comando `az account list-locations`. |

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

In questa Guida introduttiva, è stato usato Maven per creare un'applicazione web Java, configurato il [Plug-in Maven per le app web di Azure (anteprima)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), quindi distribuita un’app web Java in pacchetto con archivio web nel servizio app su Linux. Per altre informazioni sull'uso di Java con Azure, visitare il collegamento seguente.

> [!div class="nextstepaction"]
> [Azure per sviluppatori Java](https://docs.microsoft.com/java/azure/)

