---
title: "Avvio rapido: Creare un'app Java in Linux"
description: Introduzione all'uso delle app Linux nel servizio app di Azure distribuendo la prima app Java in un contenitore Linux nel servizio app.
keywords: azure, app service, Web app, linux, java, maven, avvio rapido
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e06d7d73230b17710d8c667ab89d5ead522e6de1
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254260"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Avvio rapido: Creare un'app Java in Servizio app di Azure in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida di avvio rapido illustra come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in di App Web di Azure per Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file di archivio Web (WAR) Java nel sistema operativo Linux.

> [!NOTE]
>
> La stessa operazione può essere eseguita anche con IDE diffusi come Eclipse, IntelliJ e VS Code. Vedere i documenti simili di [avvio rapido per Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [avvio rapido per Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) o [avvio rapido per VS Code](https://code.visualstudio.com/docs/java/java-webapp).
>
![App di esempio in esecuzione nel Servizio app di Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creare un'app Java

Eseguire il comando Maven seguente nel prompt di Cloud Shell per creare una nuova app denominata `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```
Passare quindi dalla directory di lavoro alla cartella del progetto:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurare il plug-in Maven

Il processo di distribuzione in Servizio app di Azure può prelevare automaticamente le credenziali dall'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è installata, il plug-in Maven consentirà di accedere tramite OAuth o credenziali del dispositivo. Controllare i dettagli sull'[autenticazione con i plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) se necessario.

Per configurare la distribuzione, eseguire il comando maven nel prompt dei comandi e usare le configurazioni predefinite premendo **INVIO** finché non viene visualizzato il prompt **Confirm (Y/N)** , quindi premere **'y'** per completare la configurazione. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Un processo di esempio ha un aspetto simile al seguente:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
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

Aprire `pom.xml` per visualizzare la configurazione aggiornata.

```bash
code pom.xml
```

È possibile modificare le configurazioni per Servizio app di Azure direttamente nel file pom, se necessario. Alcune configurazioni comuni sono elencate di seguito:

 Proprietà | Obbligatoria | Descrizione | Versione
---|---|---|---
`<schemaVersion>` | false | Specificare la versione dello schema di configurazione. I valori supportati sono: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Gruppo di risorse di Azure per App Web. | 0.1.0+
`<appName>` | true | Nome dell'App Web. | 0.1.0+
`<region>` | true | Specifica l'area in cui verrà ospitata l'app Web. Il valore predefinito è **westeurope**. Le aree valide sono disponibili nella sezione [Aree supportate](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Il piano tariffario per l'App Web. Il valore predefinito è **P1V2**.| 0.1.0+
`<runtime>` | true | Configurazione dell'ambiente di runtime. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | Configurazione della distribuzione. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Distribuire l'app

Per distribuire l'app Java in Azure, usare il comando seguente:

```bash
mvn package azure-webapp:deploy
```

Al termine della distribuzione, passare all'applicazione distribuita usando l'URL seguente nel Web browser, ad esempio `http://<webapp>.azurewebsites.net`. 

![App di esempio in esecuzione nel Servizio app di Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app in Linux è stata completata.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse dal portale eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L'esecuzione del comando può richiedere un minuto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi al database SQL di Azure con Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Connettersi al database SQL di Azure per MySQL con Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Connettersi al database SQL di Azure per PostgreSQL con Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [Integrazione continua e distribuzione continua con Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Altre risorse Azure per sviluppatori Java](/java/azure/)

> [!div class="nextstepaction"]
> [Altre informazioni sui plug-in Maven per Azure](https://github.com/microsoft/azure-maven-plugins)
