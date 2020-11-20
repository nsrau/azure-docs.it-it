---
title: Guida di avvio rapido di Advisor metriche per Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 7c2c6460207538371af2a1deada524313816793b
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523877"
---
[Documentazione di riferimento](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [Artefatto (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Una volta che si dispone di una sottoscrizione di Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Creare una risorsa di Advisor metriche"  target="_blank">creare una risorsa di Advisor metriche <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per distribuire l'istanza di Advisor metriche.  
* Un database SQL con dati di serie temporali.
  
  
> [!TIP]
> * È possibile trovare esempi di Advisor metriche per Java in [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples).
> * La risorsa di Advisor metriche può impiegare dai 10 ai 30 minuti per distribuire un'istanza del servizio da usare. Al termine della distribuzione, fare clic su **Vai alla risorsa**. Dopo la distribuzione è possibile iniziare a usare l'istanza di Advisor metriche sia con il portale Web che con l'API REST. 
> * È possibile trovare l'URL dell'API REST nella sezione **Panoramica** della risorsa nel portale di Azure. simile al seguente:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. Per altre informazioni sulla libreria client, vedere [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

### <a name="install-the-client-library"></a>Installare la libreria client

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare questa configurazione della build. Assicurarsi di includere le dipendenze del progetto.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>Creare un file Java

Creare una cartella per l'app di esempio. Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *MetricsAdvisorQuickstarts.Java.* Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples), che contiene gli esempi di codice di questo argomento.

Nella classe `MetricsAdvisorQuickstarts` dell'applicazione creare variabili per l'endpoint e la chiave della risorsa.


> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa di Advisor metriche creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. Le chiavi di sottoscrizione e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. <br><br>Per recuperare la chiave API, è necessario passare a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Selezionare i valori di **Directory**, **Sottoscrizioni** e **Area di lavoro** appropriati per la risorsa, quindi scegliere **Attività iniziali**. Sarà quindi possibile recuperare le chiavi API da [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) di Servizi cognitivi.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

Nel metodo `Main()` dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno creati in seguito.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Metrics Advisor Java SDK.

|Nome|Descrizione|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Si usa pe**: <br> - Elencare gli eventi imprevisti per anomalie <br> - Elencare la causa radice degli eventi imprevisti <br> - Recuperare i dati delle serie temporali originali e quelli arricchiti dal servizio. <br> - Elencare gli avvisi <br> - Aggiungere feedback per ottimizzare il modello |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Consente di:** <br> - Gestire i feed di dati <br> - Specificare le configurazioni di rilevamento anomalie <br> - Specificare le configurazioni degli avvisi per le anomalie <br> - Gestire gli hook  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **Quali dati inserisce Advisor metriche dall'origine dati. `DataFeed` contiene righe di:** <br> - Timestamp <br> - Zero o più dimensioni <br> - Una o più misure  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | Per `DataFeedMetric` si intende una misura quantificabile usata per monitorare e valutare lo stato di un processo aziendale specifico. Può essere una combinazione di più valori di serie temporali divisi in dimensioni. Una metrica di integrità Web può ad esempio contenere dimensioni relative al numero di utenti e al mercato statunitense. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Advisor metriche per Java:

* [Autenticare il client](#authenticate-the-client)
* [Aggiungere un feed di dati](#add-a-data-feed)
* [Controllare lo stato dell'inserimento](#check-the-ingestion-status)
* [Configurare il rilevamento anomalie](#configure-anomaly-detection)
* [Creare un hook](#create-a-hook)
* [Creare una configurazione degli avvisi](#create-an-alert-configuration)
* [Eseguire query sull'avviso](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticare il client

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Creare un client di Advisor metriche con MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Creare un client di amministrazione metriche con MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Aggiungere un feed di dati

Sostituire `sql_server_connection_string` con la stringa di connessione del server SQL e `query` con una query che restituisce i dati in corrispondenza di un singolo timestamp. Sarà anche necessario modificare i valori di `DataFeedMetric` e `DataFeedDimension` in base ai dati personalizzati.

> [!IMPORTANT]
> La query deve restituire al massimo un record per ogni combinazione di dimensioni, in corrispondenza di ogni timestamp. Tutti i record restituiti dalla query devono avere inoltre gli stessi timestamp. Advisor metriche eseguirà questa query per ogni timestamp per inserire i dati. Per altre informazioni ed esempi, vedere la [sezione delle domande frequenti sulle query](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>Controllare lo stato dell'inserimento

Questo esempio controlla lo stato di inserimento di un'origine del feed di dati specificata in precedenza.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Configurare il rilevamento anomalie 

Questo esempio illustra come impostare una configurazione di rilevamento anomalie per i dati.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Creare un hook

Questo esempio crea un hook di posta elettronica che riceve avvisi sugli eventi imprevisti per le anomalie.

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Creare una configurazione degli avvisi

Questo esempio illustra come impostare una configurazione degli avvisi per le anomalie rilevate nei dati.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>Eseguire query sull'avviso

Questo esempio illustra come eseguire query sugli avvisi attivati per una configurazione di rilevamento degli avvisi e ottenere le relative anomalie.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

È possibile compilare l'app con:

```console
gradle build
```
### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `run`:

```console
gradle run
```