---
title: Avvio rapido - Distribuire la prima applicazione Azure Spring Cloud
description: In questa guida di avvio rapido verrà distribuita un'applicazione Azure Spring Cloud in Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a4400adeff8907e2d4ff690a83c63d32da8031bd
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289322"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Questa guida di avvio rapido illustra come distribuire una semplice applicazione di microservizi Azure Spring Cloud per l'esecuzione in Azure.

>[!NOTE]
> Il supporto di Steeltoe per Azure Spring Cloud è attualmente disponibile come anteprima pubblica. Le offerte di anteprima pubblica consentono ai clienti di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.  Per altre informazioni sul supporto durante le anteprime, vedere le [domande frequenti](https://azure.microsoft.com/support/faq/) o inviare una [richiesta di supporto](../azure-portal/supportability/how-to-create-azure-support-request.md).

Seguendo questo argomento di avvio rapido, si apprenderà come:

> [!div class="checklist"]
> * Generare un progetto di base .NET Core Steeltoe
> * Effettuare il provisioning di un'istanza del servizio Azure Spring Cloud
> * Compilare e distribuire l'app con un endpoint pubblico
> * Eseguire lo streaming dei log in tempo reale

Il codice dell'applicazione usato in questo argomento di avvio rapido è una semplice app compilata con un modello di progetto API Web .NET Core. Dopo il completamento dell'esempio, l'applicazione sarà accessibile online e pronta per essere gestita tramite il portale di Azure e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Il servizio Azure Spring Cloud supporta .NET Core 3.1 e versioni successive.
* [Interfaccia della riga di comando di Azure versione 2.0.67 o successiva](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.0.67 o successiva:

```azurecli
az --version
```

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Accedere ad Azure

1. Accedere all'interfaccia della riga di comando di Azure

    ```azurecli
    az login
    ```

1. Se sono disponibili più sottoscrizioni, scegliere quella che si vuole usare per questo argomento di avvio rapido.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Generare un progetto .NET Core Steeltoe

In Visual Studio creare un'applicazione Web ASP.NET Core denominata "hello-world" con un modello di progetto API. Si noti che verrà generato automaticamente un elemento WeatherForecastController che più avanti sarà l'endpoint di test.

1. Creare una cartella per il codice sorgente del progetto e generare il progetto.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Passare alla directory del progetto.

   ```console
   cd hello-world
   ```

1. Modificare il file *appSettings.json* per aggiungere le impostazioni seguenti:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Sempre in *appsettings.json* modificare il livello di log per la categoria `Microsoft` passando da `Warning` a `Information`. Questa modifica garantisce la generazione dei log quando in un passaggio successivo si visualizzeranno i log in streaming.

   Il file *appsettings.json* ora è simile all'esempio seguente:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Aggiungere le dipendenze e un'attività `Zip` al file con estensione *csproj* :

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   I pacchetti sono per l'individuazione del servizio Steeltoe e per la libreria client di Azure Spring Cloud. L'attività `Zip` è per la distribuzione in Azure. Quando si esegue il comando `dotnet publish`, vengono generati i file binari nella cartella *publish* e questa attività comprime la cartella *publish* in un file *ZIP* caricato in Azure.

3. Nel file *Program.cs* aggiungere una direttiva `using` e il codice che usa la libreria client di Azure Spring Cloud:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   })
                   .UseAzureSpringCloudService();
   ```

4. Nel file *Startup.cs* aggiungere una direttiva `using` e il codice che usa l'individuazione del servizio Steeltoe alla fine dei metodi `ConfigureServices` e `Configure`:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Creare il progetto per assicurarsi che non siano presenti errori di compilazione.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Effettuare il provisioning di un'istanza del servizio

Nella procedura seguente viene creata un'istanza di Azure Spring Cloud usando il portale di Azure.

1. Aprire il [portale di Azure](https://ms.portal.azure.com/). 

1. Nella casella di ricerca in alto cercare *Azure Spring Cloud*.

1. Selezionare *Azure Spring Cloud* nei risultati.

   ![Icona di avvio di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Nella pagina Azure Spring Cloud fare clic su **+ Aggiungi**.

   ![Icona Aggiungi di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.  Considerare le linee guida seguenti:

   * **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa.
   * **Gruppo di risorse** : Creare un nuovo gruppo di risorse. Il nome immesso qui verrà usato come **\<resource group name\>** nei passaggi successivi.
   * **Dettagli servizio/Nome** : specificare il **\<service instance name\>** .  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
   * **Area** : selezionare l'area per l'istanza del servizio.

   ![Avvio del portale di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Selezionare **Rivedi e crea**.

## <a name="build-and-deploy-the-app"></a>Compilare e distribuire l'app

La procedura seguente compila e distribuisce il progetto creato in precedenza.

1. Assicurarsi che il prompt dei comandi sia ancora nella cartella del progetto.

1. Eseguire il comando seguente per compilare il progetto, pubblicare i file binari e archiviarli in un file *ZIP* nella cartella del progetto.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Creare un'app nell'istanza di Azure Spring Cloud con un endpoint pubblico assegnato. Usare lo stesso nome di applicazione "hello-world" specificato in *appsettings.json*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Distribuire il file *ZIP* nell'app.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   L'opzione `--main-entry` identifica il file *DLL* contenente il punto di ingresso dell'applicazione. Dopo aver caricato il file *ZIP* , il servizio estrae tutti i file e le cartelle e prova a eseguire il punto di ingresso nel file *DLL* specificato da `--main-entry`.

   Per completare la distribuzione dell'applicazione sono necessari alcuni minuti. Per verificare che sia stata distribuita, passare al pannello **App** nel portale di Azure.

## <a name="test-the-app"></a>Testare l'app

Dopo aver completato la distribuzione, accedere all'app all'URL seguente:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

L'app restituisce dati JSON simili all'esempio seguente:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Eseguire lo streaming dei log in tempo reale

Usare il comando seguente per ottenere i log in tempo reale dall'app.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

I log vengono visualizzati nell'output:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Usare `az spring-cloud app logs -h` per esplorare altri parametri e funzionalità di streaming dei log.

Per funzionalità di analisi dei log avanzate, vedere la scheda **Log** nel menu nel [portale di Azure](https://portal.azure.com/). I log hanno una latenza di pochi minuti.
[ ![Analisi dei log](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Questa guida di avvio rapido illustra come distribuire una semplice applicazione di microservizi Azure Spring Cloud per l'esecuzione in Azure. 

Il codice dell'applicazione usato in questa esercitazione è una semplice app compilata con Spring Initializr. Dopo il completamento dell'esempio, l'applicazione sarà accessibile online e pronta per essere gestita tramite il portale di Azure.

Questa guida di avvio rapido illustra come:

> [!div class="checklist"]
> * Generare un progetto Spring Cloud di base
> * Effettuare il provisioning di un'istanza del servizio
> * Compilare e distribuire l'app con un endpoint pubblico
> * Eseguire lo streaming dei log in tempo reale

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* [Installare JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Generare un progetto Spring Cloud

Usare prima di tutto [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) per generare un progetto di esempio con le dipendenze consigliate per Azure Spring Cloud. L'immagine seguente mostra la configurazione di Initializr per questo progetto di esempio.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Pagina di Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Dopo aver impostato tutte le dipendenze, fare clic su **Generate** (Genera). Scaricare e decomprimere il pacchetto, quindi creare un controller Web per una semplice applicazione Web aggiungendo `src/main/java/com/example/hellospring/HelloController.java` come indicato di seguito:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Effettuare il provisioning di un'istanza di Azure Spring Cloud

Nella procedura seguente viene creata un'istanza di Azure Spring Cloud usando il portale di Azure.

1. In una nuova scheda accedere al [portale di Azure](https://ms.portal.azure.com/). 

2. Nella casella di ricerca in alto cercare *Azure Spring Cloud*.

3. Selezionare *Azure Spring Cloud* nei risultati.

    ![Icona di avvio di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Nella pagina di Azure Spring Cloud fare clic su **+ Aggiungi**.

    ![Icona Aggiungi di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.  Considerare le linee guida seguenti:
    - **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa.
    - **Gruppo di risorse** : è consigliabile creare un nuovo gruppo di risorse per le nuove risorse. Verrà usato in passaggi successivi come **\<resource group name\>** .
    - **Dettagli servizio/Nome** : specificare il **\<service instance name\>** .  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - **Località** : selezionare l'area per l'istanza del servizio.

    ![Avvio del portale di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Fare clic su **Rivedi e crea**.

## <a name="build-and-deploy-the-app"></a>Compilare e distribuire l'app
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Nella procedura seguente l'applicazione viene compilata e distribuita usando l'interfaccia della riga di comando di Azure. Dalla radice del progetto eseguire il comando seguente.

1. Compilare il progetto tramite Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Se non è già stata installata, installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Creare l'app con l'endpoint pubblico assegnato:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Distribuire il file JAR per l'app (`target\hellospring-0.0.1-SNAPSHOT.jar` in Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Per completare la distribuzione dell'applicazione sono necessari alcuni minuti. Per verificare che sia stata distribuita, passare al pannello **App** nel portale di Azure. Si dovrebbe vedere lo stato dell'applicazione.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

La procedura seguente usa il plug-in IntelliJ per Azure Spring Cloud per distribuire l'app di esempio in IntelliJ IDEA.  

### <a name="import-project"></a>Importare il progetto

1. Aprire la finestra di dialogo di **benvenuto** di IntelliJ e selezionare **Import Project** (Importa progetto) per aprire l'importazione guidata.
1. Selezionare la cartella `hellospring`.

    ![Importare il progetto](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Distribuire l'app
Per eseguire la distribuzione in Azure, occorre accedere con l'account Azure e scegliere la sottoscrizione.  Per informazioni dettagliate sull'accesso, vedere [Installazione e accesso](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Fare clic con il pulsante destro del mouse sul progetto nella finestra di esplorazione progetti IntelliJ, quindi scegliere **Azure** -> **Deploy to Azure Spring Cloud** (Distribuisci in Azure Spring Cloud).

    [ ![Distribuzione in Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Accettare il nome dell'app nel campo **Name** (Nome). Il **nome** fa riferimento alla configurazione, non al nome dell'app. Non è in genere necessario modificarlo.
1. Nella casella di testo **Artifact** (Artefatto) selezionare *hellospring-0.0.1-SNAPSHOT.jar*.
1. Nella casella di testo **Subscription** (Sottoscrizione) verificare la sottoscrizione.
1. Nella casella di testo **Spring Cloud** selezionare l'istanza di Azure Spring Cloud creata in [Effettuare il provisioning di un'istanza di Azure Spring Cloud](./spring-cloud-quickstart-provision-service-instance.md).
1. Impostare **Public Endpoint** (Endpoint pubblico) su *Enable* (Abilita).
1. Nella casella di testo **App** selezionare **Create app** (Crea app).
1. Immettere *hellospring* e fare clic su **OK**.

    [ ![Distribuzione in Azure OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Avviare la distribuzione facendo clic sul pulsante **Run** (Esegui) nella parte inferiore della finestra di dialogo **Deploy Azure Spring Cloud app** (Distribuisci app Azure Spring Cloud). Il plug-in eseguirà il comando `mvn package` nell'app `hellospring` e distribuirà il file JAR generato dal comando `package`.
---

Dopo aver completato la distribuzione, è possibile accedere all'app all'indirizzo `https://<service instance name>-hellospring.azuremicroservices.io/`.

  [ ![Accesso all'app dal browser](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Streaming dei log in tempo reale

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Usare il comando seguente per ottenere i log in tempo reale dall'app.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
I log vengono visualizzati nei risultati:

[ ![Streaming dei log](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Usare `az spring-cloud app logs -h` per esplorare altri parametri e funzionalità di streaming dei log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selezionare **Azure Explorer** , quindi **Spring Cloud**.
1. Fare clic con il pulsante destro del mouse sull'app in esecuzione.
1. Selezionare **Streaming Logs** (Log in streaming) dall'elenco a discesa.
1. Selezione l'istanza.

    [ ![Selezionare log in streaming](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Il log in streaming sarà visibile nella finestra output.

    [ ![Output del log in streaming](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Per funzionalità di analisi dei log avanzate,vedere la scheda **Log** nel menu nel [portale di Azure](https://portal.azure.com/). I log hanno una latenza di pochi minuti.

[ ![Analisi dei log](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure per cui continueranno a essere addebitati i costi finché rimangono nella sottoscrizione. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse dal portale eseguendo questo comando nell'interfaccia della riga di comando di Azure:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Generare un progetto Azure Spring Cloud di base
> * Effettuare il provisioning di un'istanza del servizio
> * Compilare e distribuire l'app con un endpoint pubblico
> * Eseguire lo streaming dei log in tempo reale

Per informazioni su come usare altre funzionalità di Azure Spring Cloud, passare alla serie di argomenti di avvio rapido che distribuisce un'applicazione di esempio in Azure Spring Cloud:

> [!div class="nextstepaction"]
> [Compilare ed eseguire microservizi](spring-cloud-quickstart-sample-app-introduction.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
