---
title: Guida alla risoluzione dei problemi del cloud Spring di Azure | Microsoft Docs
description: Guida alla risoluzione dei problemi del cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 9603f4a687b55f45be2875ccaa7b801c0c5589c9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607619"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Guida alla risoluzione dei problemi comuni

Questo articolo illustra alcuni problemi comuni e le procedure per la risoluzione dei problemi per gli sviluppatori che lavorano all'interno del cloud Spring di Azure. Si consiglia anche di leggere l' [articolo delle domande frequenti](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemi relativi a disponibilità, prestazioni e applicazioni

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Non è possibile avviare l'applicazione (ad esempio, l'endpoint non può essere connesso o restituisce 502 dopo pochi tentativi)

Esportare i log in _Azure log Analytics_. La tabella per i registri applicazioni Spring è denominata `AppPlatformLogsforSpring`. Per altre informazioni, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md)

Trovare l'errore seguente nei log indica uno dei due problemi possibili:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Uno dei fagioli o una delle relative dipendenze è mancante.
* Una delle proprietà bean risulta mancante o non valida. In questo caso, è probabile che venga visualizzato `java.lang.IllegalArgumentException`.

Le associazioni al servizio possono anche causare errori di avvio dell'applicazione. Usare parole chiave correlate ai servizi associati per eseguire query sui log.  Si supponga, ad esempio, che l'applicazione disponga di un'associazione a un'istanza di MySQL impostata sull'ora di sistema locale. Se l'applicazione non viene avviata, è possibile che nel log sia presente l'errore seguente:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Per correggere questo errore, passare al `server parameters` dell'istanza di MySql e modificare `time_zone` da `SYSTEM` a `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>L'applicazione si arresta in modo anomalo o presenta un errore imprevisto

Quando si esegue il debug degli arresti anomali dell'applicazione, iniziare controllando lo stato di esecuzione e l'individuazione dell'applicazione. Passare a _gestione app_ nella portale di Azure per assicurarsi che tutte le applicazioni siano _in esecuzione_ e in _alto_.

* Se lo stato è _in esecuzione_ , ma lo stato di individuazione non è _attivo_, passare all' [applicazione non può essere registrato](#my-application-cannot-be-registered).

* Se lo stato di individuazione è _attivo_, passare a _metrica_ per verificare l'integrità dell'applicazione. Esaminare le metriche seguenti:


  - `TomcatErrorCount` (_Tomcat. Global. Error_): qui vengono conteggiate tutte le eccezioni delle applicazioni Spring. Se questo numero è elevato, passare ad _Azure log Analytics_ per esaminare i log dell'applicazione.

  - `AppMemoryMax` (_JVM. memory. max_): quantità massima di memoria disponibile per l'applicazione. Potrebbe non essere definito o cambiare nel tempo, se definito. La quantità di memoria usata e di cui è stato eseguito il commit sarà sempre minore o uguale al valore massimo, se definito. Tuttavia, un'allocazione di memoria potrebbe non riuscire con `OutOfMemoryError` se tenta di aumentare la memoria usata, in modo che il valore used sia > al valore committed anche se sempre <= al valore max. In una situazione di questo tipo, provare ad aumentare le dimensioni massime dell'heap tramite il parametro `-Xmx`.

  - `AppMemoryUsed` (_JVM. memory. used_): quantità di memoria in byte attualmente utilizzata dall'applicazione. Per un'applicazione Java di carico normale, questa serie di metriche si formerà in un modello "a dente", in cui l'utilizzo della memoria aumenta costantemente e diminuisce con incrementi di piccole dimensioni, quindi si riduce in modo significativo senza preavviso. Il modello poi si ripete. Ciò è dovuto al fatto che Garbage Collection all'interno della macchina virtuale Java, in cui le azioni di raccolta rappresentano le gocce in ' sawteeth '.
    Questa metrica è importante per identificare i problemi di memoria, ad esempio: * esplosione di memoria all'inizio * allocazione di memoria per un percorso logico specifico * perdite di memoria graduali

  Per altri dettagli, vedere [metriche](spring-cloud-concept-metrics.md).

Vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) introduttivo per iniziare a usare _Azure log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Nell'applicazione si verifica un uso elevato della CPU o un uso elevato della memoria

Se l'applicazione riscontra un utilizzo elevato di CPU/memoria, si verifica una delle due situazioni seguenti:
* Tutte le istanze dell'app presentano un utilizzo elevato di CPU/memoria
* Per alcune istanze dell'app si verifica un utilizzo elevato di CPU/memoria.

Per confermare la situazione,

1. Passare a _Metrica_ e selezionare `Service CPU Usage Percentage` o `Service Memory Used`,
2. Aggiungere un filtro `App=` per specificare quale applicazione si desidera monitorare e
3. Suddividere le metriche in base `Instance`.

Se tutte le istanze riscontrano CPU/memoria elevate, è necessario scalare l'applicazione o aumentare la quantità di CPU/memoria. Per altri dettagli, vedere [ridimensionare le applicazioni](spring-cloud-tutorial-scale-manual.md)

Se alcune delle istanze riscontrano una CPU/memoria elevata, verificare lo stato dell'istanza e il relativo stato di individuazione.

Per altri dettagli, vedere [metriche](spring-cloud-concept-metrics.md).

Se tutte le istanze sono in esecuzione, passare ad _Azure log Analytics_ per eseguire una query sui log dell'applicazione ed esaminare la logica del codice per verificare se uno di essi potrebbe influisca sul partizionamento della scala. Per altri dettagli, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md).

Vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) introduttivo per iniziare a usare _Azure log Analytics_. Eseguire una query sui log usando il [linguaggio di query kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Elenco di controllo prima dell'onboarding dell'applicazione Spring nel cloud Spring di Azure

* L'applicazione può essere eseguita localmente con la versione runtime Java specificata.
* La configurazione dell'ambiente (CPU/RAM/istanze) soddisfa i requisiti minimi impostati dal provider dell'applicazione.
* Gli elementi di configurazione hanno valori previsti. Per ulteriori informazioni, vedere [config server](spring-cloud-tutorial-config-server.md).
* Le variabili di ambiente hanno valori previsti.
* I parametri JVM hanno valori previsti.
* Si consiglia di disabilitare/rimuovere il _server di configurazione_ incorporato e il servizio _del registro di sistema Spring Service_ dal pacchetto dell'applicazione.
* Se è necessario associare risorse di Azure tramite l'_associazione al servizio_, verificare che le risorse di destinazione siano in esecuzione.

## <a name="configuration-and-management"></a>Configurazione e gestione

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Si è verificato un problema durante la creazione di un'istanza del servizio cloud di Azure Spring

Quando si prova a eseguire il provisioning di un'istanza del servizio _cloud Spring di Azure_ tramite il portale, Azure Spring cloud eseguirà la convalida.

Tuttavia, se si prova a eseguire il provisioning dell'istanza del servizio _cloud Spring di Azure_ tramite l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) o [Gestione risorse modello](https://docs.microsoft.com/azure/azure-resource-manager/),

* La sottoscrizione è attiva.
* Il percorso è [supportato](spring-cloud-faq.md) dal _cloud Spring di Azure_.
* Il gruppo di risorse per l'istanza è già stato creato.
* Il nome della risorsa è conforme alla regola di denominazione. Può contenere solo lettere minuscole, numeri e trattini. Il primo carattere deve essere una lettera. L'ultimo carattere deve essere una lettera o un numero. Il valore deve avere una lunghezza compresa tra 2 e 32 caratteri.

Se si prova a eseguire il provisioning dell'istanza del servizio _cloud Spring di Azure_ tramite il modello di gestione risorse, vedere https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates per verificare la sintassi del modello.

Il nome dell'istanza del servizio _cloud Spring di Azure_ verrà usato per richiedere un nome di sottodominio in `azureapps.io`, quindi il provisioning avrà esito negativo se il nome è in conflitto con uno esistente. Altri dettagli sono disponibili nei log attività.

### <a name="i-cannot-deploy-a-jar-package"></a>Non è possibile eseguire la distribuzione di un pacchetto JAR

Non è possibile caricare il pacchetto JAR/origine tramite il portale o il modello di Gestione risorse.

Quando si distribuisce il pacchetto dell'applicazione usando l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), verrà eseguito periodicamente il polling dello stato di avanzamento della distribuzione e, alla fine, verrà visualizzato il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Assicurarsi che l'applicazione sia in pacchetto nel [formato jar eseguibile](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)corretto. Verrà visualizzato un errore simile al seguente:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Non è possibile eseguire la distribuzione di un pacchetto di origine

Non è possibile caricare il pacchetto JAR/origine tramite il portale o il modello di Gestione risorse.

Quando si distribuisce il pacchetto dell'applicazione tramite [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), viene eseguito periodicamente il polling dello stato di avanzamento della distribuzione e al termine verrà visualizzato il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di progettazione e distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Si noti tuttavia che un'istanza del servizio _cloud Spring di Azure_ può attivare un solo processo di compilazione per un pacchetto di origine alla volta. Per altri dettagli, vedere la guida alla [distribuzione di un'applicazione](spring-cloud-quickstart-launch-app-portal.md) e all' [ambiente di staging](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Non è possibile registrare l'applicazione

Nella maggior parte dei casi, ciò si verifica quando le dipendenze o l'individuazione del servizio richieste non sono configurate correttamente nel file POM. Una volta configurata, l'endpoint server del registro di sistema del servizio predefinito verrà inserito come variabile di ambiente con l'applicazione. Le applicazioni si registrano quindi nel server del registro di sistema del servizio e individuano altri microservizi dipendenti.

Attendere almeno 2 minuti prima che un'istanza appena registrata inizi a ricevere il traffico.

Se si esegue la migrazione di una soluzione basata su Spring cloud esistente in Azure, verificare che le istanze del _server di configurazione_ e _del registro_ di sistema ad hoc siano rimosse (o disabilitate) per evitare conflitti con le istanze gestite fornite dal _cloud Spring di Azure_ .

È anche possibile controllare i log client _del registro di sistema del servizio_ in _log Analytics di Azure_. Per altri dettagli, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md)

Vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) introduttivo per iniziare a usare _Azure log Analytics_. Eseguire una query sui log usando il [linguaggio di query kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Desidero esaminare le variabili di ambiente dell'applicazione

Le variabili di ambiente informano il Framework di Azure Spring cloud, assicurando che Azure possa capire dove e come configurare i servizi che costituiscono l'applicazione.  Verificare che le variabili di ambiente siano corrette è il primo passaggio necessario per la risoluzione dei problemi potenziali.  È possibile usare l'endpoint dell'attuatore Spring boot per esaminare le variabili di ambiente.  

> [!WARNING]
> Questa procedura espone le variabili di ambiente usando l'endpoint di test.  Non continuare se l'endpoint di test è accessibile pubblicamente o se è stato assegnato un nome di dominio all'applicazione.

1. Passare a questo URL: `https://<your application test endpoint>/actuator/health`.  
    - Una risposta simile a `{"status":"UP"}` indica che l'endpoint è stato abilitato.
    - Se la risposta è negativa, includere nella `POM.xml`la dipendenza seguente:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Con l'endpoint dell'attuatore Spring Boot abilitato, passare alla portale di Azure e trovare la pagina di configurazione dell'applicazione.  Aggiungere una variabile di ambiente con il nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` e il valore `*`. 

1. Riavviare l'applicazione.

1. Passare a `https://<the test endpoint of your app>/actuator/env` e controllare la risposta.  L'aspetto dovrebbe risultare simile al seguente:

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
> Ricordarsi di annullare l'esposizione delle variabili di ambiente prima di rendere l'applicazione accessibile al pubblico.  Passare alla portale di Azure, trovare la pagina di configurazione dell'applicazione ed eliminare la variabile di ambiente: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Non è possibile trovare metriche o registri applicazione

Passare a _gestione app_ per assicurarsi che l'applicazione sia _in esecuzione_ e _su_.

Se è possibile visualizzare le metriche da _JVM_ ma nessuna metrica da _Tomcat_, verificare che la dipendenza`spring-boot-actuator` sia abilitata nel pacchetto dell'applicazione e venga avviata correttamente.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se i registri applicazioni possono essere archiviati in un account di archiviazione ma non inviati ad _Azure Log Analytics_, verificare che [l'area di lavoro sia stata configurata correttamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se si usa un livello gratuito di _log Analytics di Azure_, si noti che [il livello gratuito non fornisce contratti](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)di licenza.