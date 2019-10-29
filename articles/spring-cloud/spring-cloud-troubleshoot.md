---
title: Guida alla risoluzione dei problemi di Azure Spring Cloud | Microsoft Docs
description: Guida alla risoluzione dei problemi di Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ee51841046962a6896b4c16e651f85ff761a69fc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592477"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Guida alla risoluzione dei problemi comuni

Questo articolo contiene informazioni dettagliate su alcuni problemi comuni e sulle procedure per risolverli per gli sviluppatori che usano Azure Spring Cloud. È inoltre consigliabile leggere l'[articolo di domande frequenti](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemi di disponibilità, prestazioni e applicazione

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Non è possibile avviare l'applicazione (ad esempio, l'endpoint non può essere connesso o restituisce l'errore 502 dopo alcuni tentativi)

Esportare i log in _Azure Log Analytics_. La tabella per i registri applicazioni Spring è denominata `AppPlatformLogsforSpring`. Per altre informazioni, vedere [Analizzare log e metriche con le impostazioni di diagnostica](diagnostic-services.md)

Se nei log viene rilevato l'errore seguente, è probabile che si sia verificato uno dei due problemi seguenti:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Uno dei bean o una delle relative dipendenze risulta mancante.
* Una delle proprietà bean risulta mancante o non valida. È probabile che in questo caso venga visualizzato `java.lang.IllegalArgumentException`.

Anche i binding del servizio potrebbero generare errori nell'avvio dell'applicazione. Usare parole chiave correlate ai servizi associati per eseguire query sui log.  Si supponga ad esempio che l'applicazione includa un binding a un'istanza di MySQL impostata sull'ora di sistema locale. Se non è possibile avviare l'applicazione, il log potrebbe includere l'errore seguente:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Per correggere l'errore, passare a `server parameters` dell'istanza di MySQL e cambiare `time_zone` da `SYSTEM` a `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>L'applicazione si arresta in modo anomalo o presenta un errore imprevisto

Per iniziare il debug degli arresti anomali, controllare lo stato di esecuzione e lo stato di individuazione dell'applicazione. Nel portale di Azure aprire _Gestione app_ per verificare che lo stato di tutte le applicazioni sia _In esecuzione_ e _UP_.

* Se lo stato è _In esecuzione_ ma lo stato di individuazione non è _UP_, passare a [Non è possibile registrare l'applicazione](#my-application-cannot-be-registered).

* Se lo stato di individuazione è _UP_, passare a _Metriche_ per verificare l'integrità dell'applicazione. Esaminare le metriche seguenti:


  - `TomcatErrorCount` (_tomcat.global.error_): Tutte le eccezioni delle applicazioni Spring verranno conteggiate qui. Se questo numero è elevato, passare ad _Azure Log Analytics_ per esaminare i log dell'applicazione.

  - `AppMemoryMax` (_jvm.memory.max_): la quantità massima di memoria disponibile per l'applicazione. Potrebbe essere indefinita oppure cambiare nel tempo se definita. La quantità di memoria usata e di cui è stato eseguito il commit sarà sempre minore o uguale al valore massimo, se definito. Tuttavia, un'allocazione di memoria potrebbe non riuscire con `OutOfMemoryError` se tenta di aumentare la memoria usata, in modo che il valore used sia > al valore committed anche se sempre <= al valore max. In una situazione di questo tipo, provare ad aumentare le dimensioni massime dell'heap tramite il parametro `-Xmx`.

  - `AppMemoryUsed` (_jvm.memory.used_): la quantità di memoria in byte attualmente usata dall'applicazione. Per un'applicazione Java di carico normale, questa serie di metriche si formerà in un modello "a dente", in cui l'utilizzo della memoria aumenta costantemente e diminuisce con incrementi di piccole dimensioni, quindi si riduce in modo significativo senza preavviso. Il modello poi si ripete. Ciò è dovuto a Garbage Collection all'interno della macchina virtuale Java, in cui le azioni di raccolta rappresentano le depressioni nel profilo a 'denti di sega'.
    Questa metrica è importante per identificare i problemi di memoria, ad esempio * l'esplosione di memoria all'inizio, * l'aumento di allocazione di memoria per un percorso logico specifico, * le perdite di memoria graduali.

  Per altri dettagli, vedere [Metriche](spring-cloud-concept-metrics.md).

Vedere [questo articolo introduttivo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) per iniziare a usare _Azure Log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Nell'applicazione si verifica un uso elevato della CPU o un uso elevato della memoria

Se per l'applicazione si rileva un utilizzo elevato di CPU/memoria, si è verificata una delle situazioni seguenti:
* Tutte le istanze dell'app presentano un utilizzo elevato di CPU/memoria oppure
* Alcune istanze dell'app presentano un utilizzo elevato di CPU/memoria.

Per verificare di quale situazione si tratta:

1. Passare a _Metrica_ e selezionare `Service CPU Usage Percentage` o `Service Memory Used`,
2. Aggiungere un filtro `App=` per specificare quale applicazione si desidera monitorare e
3. Dividere la metrica in base a `Instance`.

Se l'utilizzo elevato di CPU/memoria si riscontra in tutte le istanze, è necessario aumentare le istanze dell'applicazione o aumentare la quantità di CPU/memoria. Per altre informazioni, vedere [Ridimensionare le applicazioni](spring-cloud-tutorial-scale-manual.md)

Se l'utilizzo elevato di CPU/memoria si riscontra per alcune istanze, controllare lo stato dell'istanza e il relativo stato di individuazione.

Per altre informazioni, vedere [Metriche](spring-cloud-concept-metrics.md).

Se tutte le istanze sono in esecuzione, passare ad _Azure Log Analytics_ per eseguire una query sui log applicazioni ed esaminare la logica del codice per verificare se una di esse potrebbe avere un effetto sul partizionamento della scalabilità. Per altre informazioni, vedere [Analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md).

Vedere [questo articolo introduttivo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) per iniziare a usare _Azure Log Analytics_. Eseguire query sui log usando il [linguaggio di query Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Elenco di controllo prima dell'onboarding dell'applicazione Spring in Azure Spring Cloud

* L'applicazione può essere eseguita localmente con la versione del runtime Java specificata.
* La configurazione dell'ambiente (CPU/RAM/istanze) soddisfa i requisiti minimi definiti dal provider di applicazioni.
* Gli elementi di configurazione hanno valori previsti. Per altre informazioni, vedere [Config Server](spring-cloud-tutorial-config-server.md).
* Le variabili di ambiente hanno i valori previsti.
* I parametri JVM hanno i valori previsti.
* È consigliabile disabilitare/rimuovere l'istanza incorporata di _Config Server_ e il servizio _Spring Service Registry_ dal pacchetto dell'applicazione.
* Se è necessario associare risorse di Azure tramite l'_associazione al servizio_, verificare che le risorse di destinazione siano in esecuzione.

## <a name="configuration-and-management"></a>Configurazione e gestione

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Si è verificato un problema durante la creazione di un'istanza del servizio Azure Spring Cloud

Quando si prova a effettuare il provisioning di un'istanza del servizio _Azure Spring Cloud_ tramite il portale, Azure Spring Cloud eseguirà automaticamente la convalida.

Tuttavia, se si prova a effettuare il provisioning dell'istanza del servizio _Azure Spring Cloud_ tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) oppure il [modello di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), verificare quanto segue:

* La sottoscrizione è attiva.
* La località è [supportata](spring-cloud-faq.md) da _Azure Spring Cloud_.
* Il gruppo di risorse per l'istanza è già stato creato.
* Il nome della risorsa è conforme alla regola di denominazione. Può contenere solo lettere in minuscolo, numeri e trattini. Il primo carattere deve essere una lettera. L'ultimo carattere deve essere una lettera o un numero. Il valore deve essere composto da 2-32 caratteri.

Se si prova a effettuare il provisioning dell'istanza del servizio _Azure Spring Cloud_ tramite il modello di Resource Manager, vedere https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates per controllare la sintassi del modello.

Il nome dell'istanza del servizio _Azure Spring Cloud_ verrà usato per richiedere il nome del sottodominio in `azureapps.io`, per cui il provisioning non riesce se il nome è in conflitto con uno esistente. Altri dettagli sono disponibili nei log attività.

### <a name="i-cannot-deploy-a-jar-package"></a>Non è possibile eseguire la distribuzione di un pacchetto JAR

Non è possibile caricare il pacchetto di origine/JAR tramite il portale o il modello di Resource Manager.

Quando si distribuisce il pacchetto dell'applicazione tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), viene eseguito periodicamente il polling dello stato di avanzamento della distribuzione e al termine verrà visualizzato il risultato.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Assicurarsi che il pacchetto dell'applicazione sia nel [formato JAR eseguibile corretto](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Verrà visualizzato un errore simile al seguente:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Non è possibile eseguire la distribuzione di un pacchetto di origine

Non è possibile caricare il pacchetto di origine/JAR tramite il portale o il modello di Resource Manager.

Quando si distribuisce il pacchetto dell'applicazione tramite [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), viene eseguito periodicamente il polling dello stato di avanzamento della distribuzione e al termine verrà visualizzato il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di progettazione e distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Tenere presente, tuttavia, che un'istanza del servizio _Azure Spring Cloud_ può attivare un solo processo di compilazione per un pacchetto di origine alla volta. Per altre informazioni, vedere [Distribuire un'applicazione](spring-cloud-quickstart-launch-app-portal.md) e [Guida all'ambiente di staging](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Non è possibile registrare l'applicazione

Nella maggior parte dei casi, questo problema si verifica se la configurazione di dipendenze necessarie/individuazione del servizio non è corretta nel file POM. Una volta configurato, l'endpoint del server Service Registry incorporato verrà inserito come variabile di ambiente con l'applicazione. Le applicazioni potranno quindi registrarsi con il server Service Registry e individuare altri microservizi dipendenti.

Prima che l'istanza appena registrata inizi a ricevere traffico bisognerà attendere almeno 2 minuti.

Se si esegue la migrazione di una soluzione basata su Spring Cloud esistente ad Azure, verificare che le istanze ad hoc di _Service Registry_ e _Config Server_ siano state rimosse (o disabilitate) per evitare conflitti con le istanze gestite fornite da _Azure Spring Cloud_.

È anche possibile controllare i log del client _Service Registry_ in _Azure Log Analytics_. Per altre informazioni, vedere [Analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md)

Vedere [questo articolo introduttivo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) per iniziare a usare _Azure Log Analytics_. Eseguire query sui log usando il [linguaggio di query Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Si vuole esaminare le variabili di ambiente dell'applicazione

Le variabili di ambiente informano il framework di Azure Spring Cloud, assicurando che Azure possa comprendere dove e come configurare i servizi che costituiscono l'applicazione.  Assicurare la correttezza delle variabili di ambiente è un primo passaggio necessario nella risoluzione dei potenziali problemi.  È possibile usare l'endpoint di Spring Boot Actuator per esaminare le variabili di ambiente.  

> [!WARNING]
> Questa procedura espone le variabili di ambiente usando l'endpoint di test.  Non continuare se l'endpoint di test è accessibile pubblicamente o se è stato assegnato un nome di dominio all'applicazione.

1. Passare all'URL `https://<your application test endpoint>/actuator/health`.  
    - Una risposta simile a `{"status":"UP"}` indica che l'endpoint è stato abilitato.
    - Se la risposta è negativa, includere la dipendenza seguente in `POM.xml`:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Con l'endpoint di Spring Boot Actuator abilitato, passare al portale di Azure e trovare la pagina di configurazione dell'applicazione.  Aggiungere una variabile di ambiente con il nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE' and the value `*'. 

1. Riavviare l'applicazione.

1. Passare a `https://<the test endpoint of your app>/actuator/env` ed esaminare la risposta.  L'aspetto dovrebbe risultare simile al seguente:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Trovare il nodo figlio denominato `systemEnvironment`.  Questo nodo contiene le variabili di ambiente dell'applicazione.

> [!IMPORTANT]
> Ricordarsi di annullare l'esposizione delle variabili di ambiente prima di rendere l'applicazione accessibile al pubblico.  Passare al portale di Azure, trovare la pagina di configurazione dell'applicazione ed eliminare la variabile di ambiente `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Non è possibile trovare metriche o registri applicazione

Passare a _Gestione app_ per verificare che lo stato dell'applicazione sia _In esecuzione_ e _UP_.

Se è possibile visualizzare le metriche da _JVM_ ma non altrettanto da _Tomcat_, verificare che nel pacchetto dell'applicazione la dipendenza `spring-boot-actuator` sia abilitata e venga avviata correttamente.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se i registri applicazioni possono essere archiviati in un account di archiviazione ma non inviati ad _Azure Log Analytics_, verificare che [l'area di lavoro sia stata configurata correttamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se si usa un livello gratuito di _Azure Log Analytics_, tenere presente che [il livello gratuito non prevede alcun contratto di servizio](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).