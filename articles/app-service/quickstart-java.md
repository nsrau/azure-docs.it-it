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
ms.openlocfilehash: 3c5a04bd14bdcbf250908db78c622b963f191d91
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333087"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Avvio rapido: Creare un'app Java nel Servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.  Questo argomento di avvio rapido illustra come usare l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) con il [plug-in App Web di Azure per Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file con estensione jar o war. Usare le schede per passare da istruzioni Java SE a Tomcat e viceversa.


> [!NOTE]
> La stessa operazione può essere eseguita anche con gli IDE più comuni come Eclipse e IntelliJ. Consultare i documenti simili in [Avvio rapido di Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) oppure in [Avvio rapido di Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![App di esempio in esecuzione nel Servizio app di Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creare un'app Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clonare l'esempio di progetto [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Passare alla directory del progetto completato.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Eseguire il comando Maven seguente nel prompt di Cloud Shell per creare una nuova app denominata `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Passare quindi dalla directory di lavoro alla cartella del progetto:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Configurare il plug-in Maven

Il processo di distribuzione nel servizio app di Azure userà automaticamente le credenziali di Azure dall'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è installata in locale, il plug-in Maven eseguirà l'autenticazione con OAuth o con le credenziali del dispositivo. Per altre informazioni, vedere [Autenticazione con i plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Eseguire il comando Maven seguente per configurare la distribuzione. Questo comando consentirà di configurare il sistema operativo del servizio app, la versione di Java e la versione di Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando viene visualizzata l'opzione **Sottoscrizione** , selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **App Web** , accettare l'opzione predefinita `<create>` premendo INVIO oppure selezionare un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo** , selezionare **Windows** immettendo `3`.
1. Quando viene visualizzata l'opzione **Piano tariffario** , selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8** , premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    L'output di riepilogo sarà simile al frammento riportato di seguito.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando viene visualizzata l'opzione **Sottoscrizione** , selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **App Web** , accettare l'opzione predefinita `<create>` premendo INVIO oppure selezionare un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo** , selezionare **Windows** immettendo `3`.
1. Quando viene visualizzata l'opzione **Piano tariffario** , selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8** , premendo INVIO.
1. Usare il contenitore Web predefinito, **Tomcat 8.5** , premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    L'output di riepilogo sarà simile al frammento riportato di seguito.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando viene visualizzata l'opzione **Sottoscrizione** , selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **App Web** , accettare l'opzione predefinita `<create>` premendo INVIO oppure selezionare un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo** , selezionare **Linux** premendo INVIO.
1. Quando viene visualizzata l'opzione **Piano tariffario** , selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8** , premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando viene visualizzata l'opzione **Sottoscrizione** , selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **App Web** , accettare l'opzione predefinita `<create>` premendo INVIO oppure selezionare un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo** , selezionare **Linux** premendo INVIO.
1. Quando viene visualizzata l'opzione **Piano tariffario** , selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8** , premendo INVIO.
1. Usare il contenitore Web predefinito, **Tomcat 8.5** , premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

È possibile modificare le configurazioni per il servizio app direttamente nel file `pom.xml`, se necessario. Ecco alcune configurazioni comuni:

Proprietà | Obbligatoria | Descrizione | Versione
---|---|---|---
`<schemaVersion>` | false | Specificare la versione dello schema di configurazione. I valori supportati sono: `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Specificare l'ID sottoscrizione. | 0.1.0+
`<resourceGroup>` | true | Gruppo di risorse di Azure per App Web. | 0.1.0+
`<appName>` | true | Nome dell'App Web. | 0.1.0+
`<region>` | true | Specifica l'area in cui verrà ospitata l'app Web. Il valore predefinito è **westeurope**. Le aree valide sono disponibili nella sezione [Aree supportate](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Il piano tariffario per l'App Web. Il valore predefinito è **P1V2** per il carico di lavoro di produzione, mentre **B2** è il valore minimo consigliato per le attività di sviluppo/test Java. [Scopri di più](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | Configurazione dell'ambiente di runtime. I dettagli sono disponibili [qui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | Configurazione della distribuzione. I dettagli sono disponibili [qui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Prestare attenzione ai valori di `<appName>` e `<resourceGroup>`(`helloworld-1590394316693` e `helloworld-1590394316693-rg` nella demo), perché verranno usati in seguito.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Distribuire l'app

Il plug-in Maven usa le credenziali dell'account dall'interfaccia della riga di comando di Azure per la distribuzione nei servizi app. [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli) prima di continuare.

```azurecli
az login
```

Quindi è possibile distribuire l'app Java in Azure con il comando seguente.

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
> [Connettersi al database SQL di Azure per PostgreSQL con Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurare CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informazioni sui prezzi](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Aggregare log e metriche](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Aumentare le prestazioni](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Risorse Azure per sviluppatori Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-language-java.md)
