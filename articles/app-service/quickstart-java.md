---
title: "Avvio rapido: Creare un'app Java nel Servizio app di Azure"
description: Distribuire la prima app Hello World Java nel Servizio app di Azure in pochi minuti. Il plug-in di App Web di Azure per Maven semplifica la distribuzione delle app Java.
keywords: azure, servizio app, web app, windows, linux, java, maven, avvio rapido
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182698"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Avvio rapido: Creare un'app Java nel Servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.  Questa guida di avvio rapido spiega come usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con il [plug-in di App Web di Azure per Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file di archivio Web Java (WAR).

> [!NOTE]
> In questo articolo vengono usate solo app Java in pacchetto con file WAR. Il plug-in supporta anche le applicazioni Web JAR. Vedere [Distribuire un file JAR Java SE nel servizio app di Azure in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per provarlo.

> [!NOTE]
> La stessa operazione può essere eseguita anche con gli IDE più comuni come Eclipse e IntelliJ. Consultare i documenti simili in [Avvio rapido di Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) oppure in [Avvio rapido di Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![App di esempio in esecuzione nel Servizio app di Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

Il processo di distribuzione nel servizio app di Azure può prelevare automaticamente le credenziali dall'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è installata in locale, il plug-in Maven consentirà di accedere tramite OAuth o con le credenziali del dispositivo. Controllare i dettagli sull'[autenticazione con i plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) se necessario.

È possibile eseguire il comando Maven seguente per configurare la distribuzione
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Verrà richiesto di effettuare queste selezioni 
* **Sistema operativo (impostazione predefinita: `linux`)**
* **Versione di Java (impostazione predefinita: `1.8`)**
* **Contenitore Web (impostazione predefinita: `tomcat 8.5`)** 
 
Prestare attenzione a inserire **`2`** per scegliere il sistema operativo **windows** nel primo passaggio. Per le altre opzioni di configurazione è possibile lasciare le impostazioni predefinite premendo **INVIO**. Infine, premere **`Y`** nella richiesta **Conferma (S/N)** per completare la configurazione.

Un processo di esempio ha un aspetto simile al seguente:

```console
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
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

Verrà richiesto di effettuare queste selezioni 
* **Sistema operativo (impostazione predefinita: `linux`)**
* **Versione di Java (impostazione predefinita: `Java 8`)**
* **Contenitore Web (impostazione predefinita: `Tomcat 8.5`)** 

Per tutte le opzioni di configurazione è possibile lasciare le impostazioni predefinite premendo **INVIO**. Infine, premere **`Y`** nella richiesta **Conferma (S/N)** per completare la configurazione.
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
::: zone-end

È possibile modificare le configurazioni per il servizio app direttamente nel file `pom.xml`, se necessario. Di seguito sono elencate alcune configurazioni comuni:

 Proprietà | Obbligatoria | Descrizione | Versione
---|---|---|---
`<schemaVersion>` | false | Specificare la versione dello schema di configurazione. I valori supportati sono: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Gruppo di risorse di Azure per App Web. | 0.1.0+
`<appName>` | true | Nome dell'App Web. | 0.1.0+
`<region>` | true | Specifica l'area in cui verrà ospitata l'app Web. Il valore predefinito è **westeurope**. Le aree valide sono disponibili nella sezione [Aree supportate](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Il piano tariffario per l'App Web. Il valore predefinito è **P1V2**.| 0.1.0+
`<runtime>` | true | Configurazione dell'ambiente di runtime. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | Configurazione della distribuzione. I dettagli sono disponibili [qui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

Prestare attenzione ai valori di `<appName>` e `<resourceGroup>`(`helloworld-1590394316693` e `helloworld-1590394316693-rg` nella demo), perché verranno usati in seguito.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Distribuire l'app

Il processo di distribuzione in Servizio app di Azure usa le credenziali dell'account dell'interfaccia della riga di comando di Azure. [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) prima di continuare.

```azurecli
az login
```
Quindi è possibile distribuire l'app Java in Azure con il comando seguente:

```bash
mvn package azure-webapp:deploy
```

Al termine della distribuzione, l'applicazione sarà pronta all'indirizzo `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` nella demo). Aprire l'URL con il Web browser locale. Si dovrebbe vedere

![App di esempio in esecuzione nel Servizio app di Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app è stata completata.

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
> [Risorse Azure per sviluppatori Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [Integrazione continua e distribuzione continua con Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Eseguire il mapping di un dominio personalizzato](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Altre informazioni sui plug-in Maven per Azure](https://github.com/microsoft/azure-maven-plugins)
