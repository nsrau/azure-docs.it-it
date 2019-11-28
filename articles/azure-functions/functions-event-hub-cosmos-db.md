---
title: 'Esercitazione: Usare funzioni Java con Azure Cosmos DB e Hub eventi'
description: Questa esercitazione illustra come utilizzare gli eventi di Hub eventi per effettuare aggiornamenti in Azure Cosmos DB con una funzione scritta in Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: 04cb91a62536c493240998270b5bd8d29fd331ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230617"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Esercitazione: Creare una funzione in Java con un trigger di Hub eventi e un binding di output di Azure Cosmos DB

Questa esercitazione illustra come usare Funzioni di Azure per creare una funzione Java che analizza un flusso continuo di dati su temperatura e pressione. La funzione viene attivata da eventi dell'hub eventi che rappresentano le letture dei sensori. La funzione elabora i dati degli eventi, quindi aggiunge voci sullo stato in Azure Cosmos DB.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e configurare risorse di Azure con l'interfaccia della riga di comando di Azure.
> * Creare e testare funzioni Java che interagiscono con queste risorse.
> * Distribuire le funzioni in Azure e monitorarle con Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario avere installato:

* [Java Developer Kit](https://aka.ms/azure-jdks), versione 8
* [Apache Maven](https://maven.apache.org), versione 3.0 o successive
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) se si preferisce non usare Cloud Shell
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) versione 2.6.666 o successive

> [!IMPORTANT]
> Per completare questa esercitazione, è necessario che la variabile di ambiente `JAVA_HOME` sia impostata sulla posizione in cui è installato JDK.

Se si preferisce usare direttamente il codice per questa esercitazione, vedere il repository di esempi [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Creare le risorse di Azure

In questa esercitazione sono necessarie le risorse seguenti:

* Un gruppo di risorse che contenga le altre risorse
* Uno spazio dei nomi, un hub eventi e una regola di autorizzazione di Hub eventi
* Un account, un database e una raccolta di Cosmos DB
* Un'app per le funzioni e un account di archiviazione in cui ospitarla

Le sezioni seguenti illustrano come creare queste risorse con l'interfaccia della riga di comando di Azure.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Se non si usa Cloud Shell, sarà necessario usare l'interfaccia della riga di comando di Azure in locale per accedere all'account. Usare il comando `az login` dal prompt Bash per avviare l'esperienza di accesso basata su browser. Se si ha accesso a più sottoscrizioni di Azure, impostare quella predefinita con `az account set --subscription` seguito dall'ID sottoscrizione.

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Creare quindi alcune variabili di ambiente per i nomi e la posizione delle risorse che verranno create. Usare i comandi seguenti, sostituendo i segnaposto `<value>` con valori a scelta. I valori devono essere conformi [alle regole di denominazione e alle restrizioni per le risorse di Azure](/azure/architecture/best-practices/resource-naming). Per la variabile `LOCATION`, usare uno dei valori prodotti dal comando `az functionapp list-consumption-locations`.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Queste variabili vengono usate nella parte restante di questa esercitazione. Tenere presente che queste variabili persistono esclusivamente per la durata della sessione corrente dell'interfaccia della riga di comando di Azure o di Cloud Shell. Se si usa una finestra del terminale locale diversa, sarà necessario eseguire di nuovo questi comandi, altrimenti la sessione di Cloud Shell raggiungerà il timeout.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Azure usa i gruppi di risorse per raccogliere tutte le risorse correlate dell'account. In questo modo, è possibile visualizzarle come un'unità ed eliminarle con un singolo comando quando non sono più necessarie.

Usare il comando seguente per creare un gruppo di risorse:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Creare un hub eventi

Creare quindi uno spazio dei nomi, un hub eventi e una regola di autorizzazione di Hub eventi di Azure con i comandi seguenti:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Lo spazio dei nomi di Hub eventi contiene l'hub eventi effettivo e la relativa regola di autorizzazione. La regola di autorizzazione consente alle funzioni di inviare messaggi all'hub e di restare in ascolto degli eventi corrispondenti. Una funzione invia messaggi che rappresentano dati di telemetria. Un'altra funzione resta in ascolto degli eventi, analizza i relativi dati e archivia i risultati in Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Creare un'istanza di Azure Cosmos DB

Successivamente, creare un account, un database e una raccolta di Azure Cosmos DB usando i comandi seguenti:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

Il valore `partition-key-path` partiziona i dati in base al valore `temperatureStatus` di ogni elemento. La chiave di partizione consente di aumentare le prestazioni di Cosmos DB dividendo i dati in sottoinsiemi distinti accessibili in modo indipendente.

### <a name="create-a-storage-account-and-function-app"></a>Creare un account di archiviazione e un'app per le funzioni

Successivamente, creare un account di archiviazione di Azure, richiesto da Funzioni di Azure, quindi creare l'app per le funzioni. Usare il seguente comando:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Quando il comando `az functionapp create` crea l'app per le funzioni, crea anche una risorsa di Application Insights con lo stesso nome. L'app per le funzioni viene configurata automaticamente con un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY` che la connette ad Application Insights. È possibile visualizzare i dati di telemetria dell'app dopo aver distribuito le funzioni in Azure, come descritto più avanti in questa esercitazione.

## <a name="configure-your-function-app"></a>Configurare l'app per le funzioni

Per il corretto funzionamento, l'app per le funzioni dovrà accedere alle altre risorse. Le sezioni seguenti illustrano come configurare l'app per le funzioni in modo che possa essere eseguita nel computer locale.

### <a name="retrieve-resource-connection-strings"></a>Recuperare le stringe di connessione delle risorse

Usare i comandi seguenti per recuperare le stringhe di connessione dell'account di archiviazione, dell'hub eventi e di Cosmos DB e salvarle in variabili di ambiente:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Queste variabili sono impostate sui valori recuperati dai comandi dell'interfaccia della riga di comando di Azure. Ogni comando usa una query JMESPath per estrarre la stringa di connessione dal payload JSON restituito. Le stringhe di connessione vengono visualizzate anche usando `echo` in modo da verificare che siano state recuperate correttamente.

### <a name="update-your-function-app-settings"></a>Aggiornare le impostazioni dell'app per le funzioni

Successivamente, usare il comando seguente per trasferire i valori delle stringhe di connessione nelle impostazioni dell'app nell'account di Funzioni di Azure:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Le risorse di Azure sono ora state create e configurate per funzionare correttamente insieme.

## <a name="create-and-test-your-functions"></a>Creare e testare le funzioni

Successivamente, creare un progetto nel computer locale, aggiungere codice Java e testarlo. Usare comandi compatibili con il plug-in Maven per Funzioni di Azure e con Azure Functions Core Tools. Le funzioni verranno eseguite in locale, ma useranno le risorse basate sul cloud create. Dopo aver configurato le funzioni per l'esecuzione in locale, è possibile usare Maven per distribuirle nel cloud e osservare l'accumulo di dati e analisi.

Se le risorse sono state create con Cloud Shell, non verranno connesse ad Azure in locale. In questo caso, usare il comando `az login` per avviare il processo di accesso basato su browser. Quindi, se necessario, impostare la sottoscrizione predefinita con `az account set --subscription` seguito dall'ID sottoscrizione. Infine, eseguire i comandi seguenti per ricreare alcune variabili di ambiente nel computer locale. Sostituire i segnaposto `<value>` con gli stessi valori usati in precedenza.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Creare un progetto per le funzioni locale

Usare il comando Maven seguente per creare un progetto di funzioni e aggiungere le dipendenze necessarie.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Questo comando genera diversi file all'interno di una cartella `telemetry-functions`:

* Un file `pom.xml` da usare con Maven
* Un file `local.settings.json` per mantenere le impostazioni dell'app per i test locali
* Un file `host.json` che abilita il bundle di estensioni di Funzioni di Azure, necessario per il binding di output di Cosmos DB nella funzione di analisi dei dati
* Un file `Function.java` che include un'implementazione predefinita delle funzioni
* Alcuni file di test non necessari per questa esercitazione

Per evitare errori di compilazione, sarà necessario eliminare i file di test. Eseguire i comandi seguenti per passare alla nuova cartella del progetto ed eliminare la cartella di test:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Recuperare le impostazioni dell'app per le funzioni per l'uso locale

Per i test locali, sarà necessario fornire al progetto di funzioni le stringhe di connessioni aggiunte in precedenza all'app per le funzioni in Azure in questa esercitazione. Usare il comando seguente di Azure Functions Core Tools, che recupera tutte le impostazioni dell'app per le funzioni archiviate nel cloud e le aggiunge al file `local.settings.json`:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Aggiungere il codice Java

Successivamente, aprire il file `Function.java` e sostituirne il contenuto con il codice seguente.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Come si può notare, questo file contiene due funzioni, `generateSensorData` e `processSensorData`. La funzione `generateSensorData` simula un sensore che invia le letture di temperatura e pressione all'hub eventi. Un trigger timer esegue la funzione ogni 10 secondi e un binding di output dell'hub eventi invia il valore restituito all'hub eventi.

Quando l'hub eventi riceve il messaggio, genera un evento. La funzione `processSensorData` viene eseguita quando riceve l'evento. Elabora quindi i dati dell'evento e usa un binding di output di Azure Cosmos DB per inviare i risultati ad Azure Cosmos DB.

I dati usati da queste funzioni vengono archiviati usando una classe denominata `TelemetryItem`, che sarà necessario implementare. Creare un nuovo file denominato `TelemetryItem.java` nella stessa posizione di `Function.java` e aggiungere il codice seguente:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Esecuzione locale

È ora possibile creare ed eseguire le funzioni in locale e visualizzare i dati in Azure Cosmos DB.

Eseguire i comandi Maven seguenti per creare ed eseguire le funzioni:

```bash
mvn clean package
mvn azure-functions:run
```

Dopo alcuni messaggi di creazione e avvio, verrà visualizzato un output simile all'esempio seguente per ogni esecuzione delle funzioni:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

È quindi possibile aprire il [portale di Azure](https://portal.azure.com) e passare all'account Azure Cosmos DB. Selezionare **Esplora dati**, espandere **TelemetryInfo**, quindi selezionare **Elementi** per visualizzare i dati all'arrivo.

![Esplora dati di Cosmos DB](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Distribuire Azure e visualizzare i dati di telemetria dell'app

Infine, è possibile distribuire l'app in Azure e verificare se continua a funzionare allo stesso modo che in locale.

Distribuire il progetto in Azure con il comando seguente:

```bash
mvn azure-functions:deploy
```

Le funzioni vengono ora eseguite in Azure e continuano ad accumulare i dati in Azure Cosmos DB. È possibile visualizzare l'app per le funzioni distribuita nel portale di Azure e visualizzare i dati di telemetria dell'app tramite la risorsa di Application Insights connessa, come illustrato negli screenshot seguenti:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Prestazioni:**

![riquadro Prestazioni di Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato le operazioni relative alle risorse di Azure create in questa esercitazione, è possibile eliminarle usando il comando seguente:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare una funzione di Azure che gestisce gli eventi di Hub eventi e aggiorna un Cosmos DB. Per altre informazioni, vedere la [guida per sviluppatori Java per funzioni di Azure](/azure/azure-functions/functions-reference-java). Per informazioni sulle annotazioni usate, vedere le informazioni di riferimento su [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation).

In questa esercitazione sono state usate variabili di ambiente e impostazioni di applicazioni per archiviare segreti come le stringhe di connessione. Per informazioni sull'archiviazione di questi segreti in Azure Key Vault, vedere [Usare i riferimenti a Key Vault per il Servizio app e Funzioni di Azure](/azure/app-service/app-service-key-vault-references).

A questo punto, vedere come usare il processo CI/CD di Azure Pipelines per la distribuzione automatizzata:

> [!div class="nextstepaction"]
> [Creare e distribuire Java in Funzioni di Azure](/azure/devops/pipelines/ecosystems/java-function)
