---
title: Usare Java e Gradle per pubblicare una funzione in AzureUse Java and Gradle to publish a function to Azure
description: Creare e pubblicare una funzione attivata da HTTP in Azure con Java e Gradle.Create and publish an HTTP-triggered function to Azure with Java and Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 5e18e035bd237fd489b715986e58d7ede726348d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886603"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Usare Java e Gradle per creare e pubblicare una funzione in AzureUse Java and Gradle to create and publish a function to Azure

Questo articolo illustra come compilare e pubblicare un progetto di funzione Java in Funzioni di Azure con lo strumento da riga di comando Gradle.This article shows you how to build and publish a Java function project to Azure Functions with the Gradle command-line tool. Al termine, il codice della funzione viene eseguito in Azure in un [piano di hosting serverless](functions-scale.md#consumption-plan) e viene attivato da una richiesta HTTP. 

> [!NOTE]
> Se Gradle non è il tuo strumento di sviluppo preferito, dai un'occhiata ai nostri tutorial simili per gli sviluppatori Java che utilizzano [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) e [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Prerequisiti

Per sviluppare funzioni con Java, è necessario che siano installati gli elementi seguenti:

- [Java Developer Kit](https://aka.ms/azure-jdks), versione 8
- [Interfaccia della riga di comando di Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successive
- [Gradle](https://gradle.org/), versione 4.10 e superiore

È anche necessaria una sottoscrizione di Azure attiva. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Per completare questa guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

## <a name="prepare-a-functions-project"></a>Preparare un progetto di funzioniPrepare a Functions project

Utilizzare il comando seguente per clonare il progetto di esempio:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Aprire `build.gradle` e `appName` modificare nella sezione seguente un nome univoco per evitare conflitti di nomi di dominio durante la distribuzione in Azure.Open and change the in the following section to a unique name to avoid domain name conflict when deploying to Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Aprire il nuovo file Function.java dal percorso *src/main/java* in un editor di testo e rivedere il codice generato. Questo codice è una funzione [attivata da HTTP](functions-bindings-http-webhook.md) che restituisce il corpo della richiesta. 

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Eseguire il comando seguente per compilare quindi eseguire il progetto di funzione:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Quando si esegue il progetto in locale, viene visualizzato un output di Azure Functions Core Tools simile al seguente:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Attivare la funzione dalla riga di comando utilizzando il seguente comando cURL in una nuova finestra del terminale:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

L'output previsto è il seguente:

<pre>
Hello AzureFunctions!
</pre>

La [chiave della funzione](functions-bindings-http-webhook-trigger.md#authorization-keys) non è necessaria durante l'esecuzione in locale.  
Usare `Ctrl+C` nel terminal per interrompere il codice funzione.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

La prima volta che si distribuisce la nuova app per le funzioni, in Azure vengono create un'app per le funzioni e le risorse correlate. Prima di procedere alla distribuzione, è necessario usare il comando [az login](/cli/azure/authenticate-azure-cli) dell'interfaccia della riga di comando di Azure per accedere alla sottoscrizione di Azure. 

```azurecli
az login
```

> [!TIP]
> Se l'account può accedere a più sottoscrizioni, usare [az account set](/cli/azure/account#az-account-set) per impostare quella predefinita per questa sessione. 

Usare il comando seguente per distribuire il progetto in una nuova app per le funzioni. 

```bash
gradle azureFunctionsDeploy
```

In questo modo vengono create le risorse seguenti in Azure, in base ai valori nel file build.gradle:This creates the following resources in Azure, based on the values in the build.gradle file:

+ Gruppo di risorse. Con il nome _resourceGroup_ specificato.
+ Account di archiviazione. Richiesto da Funzioni. Il nome viene generato in modo casuale in base ai requisiti di denominazione degli account di archiviazione.
+ Piano di servizio app. Hosting del piano di consumo senza server per l'app per le funzioni _nell'appRegion_specificata. Il nome viene generato in modo casuale.
+ App per le funzioni. Un'app per le funzioni è l'unità di distribuzione ed esecuzione per le funzioni. Il nome è _appName_, con l'aggiunta finale di un numero generato in modo casuale. 

La distribuzione inserisce inoltre i file di progetto in un pacchetto e li distribuisce nella nuova app per le funzioni tramite [ZipDeploy](functions-deployment-technologies.md#zip-deploy), con la modalità run-from-package abilitata.

Poiché il trigger HTTP pubblicato usa `authLevel = AuthorizationLevel.FUNCTION`, è necessario ottenere la chiave della funzione per chiamare l'endpoint della funzione tramite HTTP. Il modo più semplice per farlo è tramite il [portale di Azure].

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Ottenere l'URL del trigger HTTP

È possibile ottenere l'URL necessario per attivare la funzione, con la chiave della funzione, nel portale di Azure. 

1. Passare al [portale di Azure], accedere, digitare il nome _appName_ dell'app per le funzioni nella casella **Cerca** nella parte superiore della pagina, quindi premere INVIO.
 
1. Nell'app per le funzioni espandere **Funzioni (sola lettura)**, scegliere la funzione, quindi selezionare **</> Recupera URL della funzione** in alto a destra. 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Scegliere il **valore predefinito (chiave della funzione)** e selezionare **Copia**. 

È ora possibile usare l'URL copiato per accedere alla funzione.

## <a name="verify-the-function-in-azure"></a>Verificare la funzione in Azure

Per verificare l'app per le funzioni in esecuzione in Azure tramite `cURL`, sostituire l'URL dell'esempio seguente con l'URL copiato dal portale.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Viene inviata una richiesta POST all'endpoint della funzione con `AzureFunctions` nel corpo. Verrà visualizzata la risposta seguente.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Java con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-java.md)


[Interfaccia della riga di comando di Azure]: /cli/azure
[Portale di Azure]: https://portal.azure.com
