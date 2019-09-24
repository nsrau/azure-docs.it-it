---
title: Creare un'app Web Java in Linux - Servizio app di Azure
description: Questa guida introduttiva spiega come distribuire la prima app Java Hello World nel servizio app di Azure in pochi minuti.
keywords: azure, app service, Web app, linux, java, maven, avvio rapido
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 2d486a5e181e9131ef7a1e91f52018fb2be82dc1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105236"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Guida introduttiva: Creare un'app Java in Servizio app di Azure in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questo argomento di avvio rapido illustra come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in Maven per il Servizio app di Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file di archivio Web (WAR) Java nel sistema operativo Linux.

> [!NOTE]
>
> La stessa operazione può essere eseguita anche con gli IDE più comuni come Eclipse e IntelliJ. Consultare i documenti simili in [Avvio rapido di Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) oppure in [Avvio rapido di Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![App di esempio in esecuzione nel Servizio app di Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

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
        <version>1.7.0</version>       
    </plugin>
</plugins>
```

Il processo di distribuzione in Servizio app di Azure usa le credenziali dell'account dell'interfaccia della riga di comando di Azure. [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) prima di continuare.

```azurecli
az login
```

Successivamente è possibile configurare la distribuzione, eseguire il comando maven `mvn azure-webapp:config` nel prompt dei comandi e usare le configurazioni predefinite premendo **INVIO** finché non viene visualizzato il prompt **Confirm (Y/N)** , quindi premere **'y'** e la configurazione è pronta.

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> In questo articolo vengono usate solo app Java in pacchetto con file WAR. Il plug-in supporta anche le applicazioni Web JAR. Vedere [Distribuire un file JAR Java SE nel servizio app di Azure in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per provarlo.

Passare nuovamente a `pom.xml` per visualizzare che la configurazione del plug-in è aggiornata. Se necessario, è possibile modificare altre configurazioni per Servizio app di Azure direttamente nel file .pom. Alcune configurazioni comuni sono elencate di seguito:

 Proprietà | Obbligatoria | DESCRIZIONE | Versione
---|---|---|---
`<schemaVersion>` | false | Specificare la versione dello schema di configurazione. I valori supportati sono: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Gruppo di risorse di Azure per App Web. | 0.1.0+
`<appName>` | true | Nome dell'App Web. | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Specifica l'area in cui verrà ospitata l'App Web; il valore predefinito è **westus**. Le aree valide sono disponibili nella sezione [Aree supportate](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region). | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Il piano tariffario per l'App Web. Il valore predefinito è **P1V2**.| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | Configurazione dell'ambiente di runtime. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | Configurazione della distribuzione. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | 0.1.0+

## <a name="deploy-the-app"></a>Distribuire l'app

Per distribuire l'app Java in Azure, usare il comando seguente:

```bash
mvn package azure-webapp:deploy
```

Al termine della distribuzione, passare all'applicazione distribuita usando l'URL seguente nel Web browser, ad esempio `http://<webapp>.azurewebsites.net`. 

![App di esempio in esecuzione nel Servizio app di Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app in Linux è stata completata.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L'esecuzione del comando può richiedere un minuto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App aziendale Java con PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Integrazione continua e distribuzione continua con Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Altre risorse Azure per sviluppatori Java](/java/azure/)
