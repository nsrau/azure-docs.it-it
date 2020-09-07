---
title: Avvio rapido - Distribuire la prima applicazione Azure Spring Cloud
description: In questa guida di avvio rapido verrà distribuita un'applicazione Azure Spring Cloud in Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260551"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud

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

* [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Generare un progetto Spring Cloud

Usare prima di tutto [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) per generare un progetto di esempio con le dipendenze consigliate per Azure Spring Cloud. L'immagine seguente mostra la configurazione di Initializr per questo progetto di esempio.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
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
    - **Gruppo di risorse**: è consigliabile creare un nuovo gruppo di risorse per le nuove risorse. Verrà usato in passaggi successivi come **\<resource group name\>** .
    - **Dettagli servizio/Nome**: specificare il **\<service instance name\>** .  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - **Località**: selezionare l'area per l'istanza del servizio.

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

1. Distribuire il file JAR per l'app:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Per completare la distribuzione dell'applicazione sono necessari alcuni minuti. Per verificare che sia stata distribuita, passare al pannello **App** nel portale di Azure. Si dovrebbe vedere lo stato dell'applicazione.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

La procedura seguente usa il plug-in IntelliJ per Azure Spring Cloud per distribuire l'app di esempio in IntelliJ IDEA.  

### <a name="import-project"></a>Importare il progetto

1. Aprire la finestra di dialogo di **benvenuto** di IntelliJ e selezionare **Import Project** (Importa progetto) per aprire l'importazione guidata.
1. Selezionare la cartella `hellospring`.

    ![Importare il progetto](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Distribuire l'app
Per eseguire la distribuzione in Azure, occorre accedere con l'account Azure e scegliere la sottoscrizione.  Per informazioni dettagliate sull'accesso, vedere [Installazione e accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Fare clic con il pulsante destro del mouse sul progetto nella finestra di esplorazione progetti IntelliJ, quindi scegliere **Azure** -> **Deploy to Azure Spring Cloud** (Distribuisci in Azure Spring Cloud).

    [ ![Distribuzione in Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Accettare il nome dell'app nel campo **Name** (Nome). Il **nome** fa riferimento alla configurazione, non al nome dell'app. Non è in genere necessario modificarlo.
1. Nella casella di testo **Artifact** (Artefatto) selezionare *hellospring-0.0.1-SNAPSHOT.jar*.
1. Nella casella di testo **Subscription** (Sottoscrizione) verificare la sottoscrizione.
1. Nella casella di testo **Spring Cloud** selezionare l'istanza di Azure Spring Cloud creata in [Effettuare il provisioning di un'istanza di Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
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

1. Selezionare **Azure Explorer**, quindi **Spring Cloud**.
1. Fare clic con il pulsante destro del mouse sull'app in esecuzione.
1. Selezionare **Streaming Logs** (Log in streaming) dall'elenco a discesa.
1. Selezione l'istanza.

    [ ![Selezionare log in streaming](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Il log in streaming sarà visibile nella finestra output.

    [ ![Output del log in streaming](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Per funzionalità di analisi dei log avanzate,vedere la scheda **Log** nel menu nel [portale di Azure](https://portal.azure.com/). I log hanno una latenza di pochi minuti.

[ ![Analisi dei log](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse
Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse dal portale eseguendo questo comando in Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Generare un progetto Azure Spring Cloud di base
> * Effettuare il provisioning di un'istanza del servizio
> * Compilare e distribuire l'app con un endpoint pubblico
> * Streaming dei log in tempo reale
## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Compilare ed eseguire microservizi](spring-cloud-quickstart-sample-app-introduction.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
