---
title: Guida alla risoluzione dei problemi di Azure Spring Cloud | Microsoft Docs
description: Guida alla risoluzione dei problemi di Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: af3b0b6113833dfd36be8b604b6b3d3e7b33fe5f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151135"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Risolvere i problemi comuni di Azure Spring cloud

Questo articolo fornisce istruzioni per la risoluzione dei problemi di sviluppo di Azure Spring cloud. Per altre informazioni, vedere [domande frequenti su Azure Spring cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemi di disponibilità, prestazioni e applicazione

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Non è possibile avviare l'applicazione (ad esempio, l'endpoint non può essere connesso o restituisce 502 dopo alcuni tentativi)

Esportare i log in Azure Log Analytics. La tabella per i registri delle applicazioni Spring è denominata *AppPlatformLogsforSpring*. Per altre informazioni, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md).

Nei log potrebbe essere visualizzato il messaggio di errore seguente:

> "org. springframework. Context. ApplicationContextException: Impossibile avviare il server Web"

Il messaggio indica uno dei due problemi possibili: 
* Uno dei bean o una delle relative dipendenze risulta mancante.
* Una delle proprietà bean risulta mancante o non valida. In questo caso, verrà probabilmente visualizzato "Java. lang. IllegalArgumentException".

Le associazioni al servizio potrebbero anche causare errori di avvio dell'applicazione. Per eseguire una query sui log, usare parole chiave correlate ai servizi associati. Si supponga, ad esempio, che l'applicazione disponga di un'associazione a un'istanza di MySQL impostata sull'ora di sistema locale. Se l'avvio dell'applicazione non riesce, è possibile che nel log venga visualizzato il seguente messaggio di errore:

> "Java. SQL. SQLException: il valore ' Coordinated Universal Time ' del fuso orario del server non è riconosciuto o rappresenta più di un fuso orario."

Per correggere l'errore, passare alla `server parameters` dell'istanza di MySQL e modificare il valore `time_zone` da *System* a *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>L'applicazione si arresta in modo anomalo o presenta un errore imprevisto

Quando si esegue il debug degli arresti anomali dell'applicazione, iniziare controllando lo stato di esecuzione e l'individuazione dell'applicazione. A tale scopo, passare a _gestione app_ nella portale di Azure per assicurarsi che gli Stati di tutte le applicazioni siano _in esecuzione_ e in _alto_.

* Se lo stato è _in esecuzione_ , ma lo stato di individuazione non è _attivo_, passare alla sezione ["non è possibile registrare l'applicazione"](#my-application-cant-be-registered) .

* Se lo stato di individuazione è _attivo_, passare a metrica per verificare l'integrità dell'applicazione. Esaminare le metriche seguenti:


  - `TomcatErrorCount` (_Tomcat. Global. Error_): qui vengono conteggiate tutte le eccezioni delle applicazioni Spring. Se questo numero è elevato, passare ad Azure Log Analytics per esaminare i log dell'applicazione.

  - `AppMemoryMax` (_JVM. memory. max_): quantità massima di memoria disponibile per l'applicazione. Il valore può essere indefinito o potrebbe cambiare nel tempo, se definito. Se è definito, la quantità di memoria utilizzata e di cui è stato eseguito il commit è sempre minore o uguale a max. Tuttavia, un'allocazione di memoria potrebbe avere esito negativo con un messaggio `OutOfMemoryError` se l'allocazione tenta di aumentare la memoria utilizzata, in modo che venga *utilizzata > commit*, anche se *utilizzata < = max* è ancora true. In una situazione di questo tipo, provare ad aumentare le dimensioni massime dell'heap utilizzando il parametro `-Xmx`.

  - `AppMemoryUsed` (_JVM. memory. used_): quantità di memoria in byte attualmente usata dall'applicazione. Per un'applicazione Java Load normale, questa serie metrica forma un modello a *dente di sega* , in cui l'utilizzo della memoria aumenta costantemente e diminuisce in piccoli incrementi e si abbassa improvvisamente, quindi il modello si ripete. Questa serie metrica si verifica a causa di Garbage Collection all'interno di una macchina virtuale Java, in cui le azioni di raccolta rappresentano le gocce sul modello a dente.
    
    Questa metrica è importante per facilitare l'identificazione dei problemi di memoria, ad esempio:
    * Un'esplosione di memoria all'inizio.
    * Allocazione di memoria per un percorso logico specifico.
    * Perdite di memoria graduali.

  Per altre informazioni, vedere [metriche](spring-cloud-concept-metrics.md).

Per altre informazioni su Azure Log Analytics, vedere [Introduzione ai log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Nell'applicazione si verifica un uso elevato della CPU o un uso elevato della memoria

Se l'applicazione riscontra un utilizzo elevato della CPU o della memoria, si verifica una delle due situazioni seguenti:
* Per tutte le istanze dell'app si verifica un utilizzo elevato della CPU o della memoria.
* Per alcune istanze dell'app si verifica un utilizzo elevato della CPU o della memoria.

Per verificare quale situazione si applica, eseguire le operazioni seguenti:

1. Passare a **metrica**, quindi selezionare la percentuale di **utilizzo della CPU del servizio** o la **memoria del servizio usata**.
2. Aggiungere un filtro **app =** per specificare quale applicazione si desidera monitorare.
3. Suddividere le metriche in base all' **istanza**.

Se per *tutte le istanze* si verifica un utilizzo elevato della CPU o della memoria, è necessario scalare l'applicazione o aumentare l'utilizzo della CPU o della memoria. Per altre informazioni, vedere [esercitazione: ridimensionare un'applicazione nel cloud Spring di Azure](spring-cloud-tutorial-scale-manual.md).

Se *alcune istanze* riscontrano un utilizzo elevato della CPU o della memoria, controllare lo stato dell'istanza e il relativo stato di individuazione.

Per altre informazioni, vedere [metriche per il cloud Spring di Azure](spring-cloud-concept-metrics.md).

Se tutte le istanze sono in esecuzione, passare ad Azure Log Analytics per eseguire una query sui log dell'applicazione ed esaminare la logica del codice. Ciò consentirà di verificare se uno di essi potrebbe influire sul partizionamento di ridimensionamento. Per altre informazioni, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md).

Per altre informazioni su Azure Log Analytics, vedere [Introduzione ai log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Eseguire una query sui log usando il [linguaggio di query kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Elenco di controllo per la distribuzione dell'applicazione Spring nel cloud Spring di Azure

Prima di eseguire l'onboarding dell'applicazione, verificare che soddisfi i criteri seguenti:

* L'applicazione può essere eseguita localmente con la versione del runtime Java specificata.
* La configurazione dell'ambiente (CPU/RAM/istanze) soddisfa i requisiti minimi definiti dal provider di applicazioni.
* Gli elementi di configurazione hanno i valori previsti. Per altre informazioni, vedere [Config Server](spring-cloud-tutorial-config-server.md).
* Le variabili di ambiente hanno i valori previsti.
* I parametri JVM hanno i valori previsti.
* Si consiglia di disabilitare o rimuovere il server di _configurazione_ incorporato e i servizi _del registro di sistema Spring Service_ dal pacchetto dell'applicazione.
* Se è necessario associare risorse di Azure tramite l'_associazione al servizio_, verificare che le risorse di destinazione siano in esecuzione.

## <a name="configuration-and-management"></a>Configurazione e gestione

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Si è verificato un problema durante la creazione di un'istanza del servizio cloud di Azure Spring

Quando si configura un'istanza del servizio cloud di Azure Spring usando il portale di Azure, Azure Spring cloud esegue automaticamente la convalida.

Tuttavia, se si tenta di configurare l'istanza del servizio cloud Spring di Azure usando l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) o il [modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), verificare che:

* La sottoscrizione è attiva.
* Il percorso è [supportato](spring-cloud-faq.md) dal cloud Spring di Azure.
* Il gruppo di risorse per l'istanza è già stato creato.
* Il nome della risorsa è conforme alla regola di denominazione. Deve contenere solo lettere minuscole, numeri e trattini. Il primo carattere deve essere una lettera. L'ultimo carattere deve essere una lettera o un numero. Il valore deve contenere da 2 a 32 caratteri.

Se si vuole configurare l'istanza del servizio cloud Spring di Azure usando il modello di Gestione risorse, vedere prima di tutto come [comprendere la struttura e la sintassi dei modelli Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Il nome dell'istanza del servizio cloud Spring di Azure verrà usato per richiedere un nome di sottodominio in `azureapps.io`, quindi l'installazione avrà esito negativo se il nome è in conflitto con uno esistente. È possibile trovare altri dettagli nei log attività.

### <a name="i-cant-deploy-a-jar-package"></a>Non è possibile distribuire un pacchetto JAR

Non è possibile caricare il pacchetto di file di archivio Java (JAR)/Source usando il portale di Azure o il modello di Gestione risorse.

Quando si distribuisce il pacchetto dell'applicazione usando l' [interfaccia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)della riga di comando di Azure, l'interfaccia della riga di comando di Azure esegue periodicamente il polling dello stato della distribuzione e, alla fine, Visualizza il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Assicurarsi che l'applicazione sia in pacchetto nel [formato jar eseguibile](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)corretto. Se il pacchetto non viene inserito correttamente, verrà visualizzato un messaggio di errore simile al seguente:

> "Errore: jarFile/jar/38bc8ea1-a6bb-4736-8E93-e8f3b52c8714 non valido o danneggiato"

### <a name="i-cant-deploy-a-source-package"></a>Non è possibile distribuire un pacchetto di origine

Non è possibile caricare il pacchetto JAR/Source usando il portale di Azure o il modello di Gestione risorse.

Quando si distribuisce il pacchetto dell'applicazione usando l' [interfaccia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)della riga di comando di Azure, l'interfaccia della riga di comando di Azure esegue periodicamente il polling dello stato della distribuzione e, alla fine, Visualizza il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di progettazione e distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Si noti tuttavia che un'istanza del servizio cloud Spring di Azure può attivare un solo processo di compilazione per un pacchetto di origine alla volta. Per altre informazioni, vedere [distribuire un'applicazione](spring-cloud-quickstart-launch-app-portal.md) e [configurare un ambiente di gestione temporanea nel cloud Spring di Azure](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Non è possibile registrare l'applicazione

Nella maggior parte dei casi, questa situazione si verifica quando le *dipendenze obbligatorie* e l' *individuazione del servizio* non sono configurate correttamente nel file POM (Project Object Model). Una volta configurato, l'endpoint server del registro di sistema del servizio predefinito viene inserito come variabile di ambiente con l'applicazione. Le applicazioni si registrano quindi nel server del registro di sistema del servizio e individuano altri microservizi dipendenti.

Attendere almeno due minuti prima che un'istanza appena registrata inizi a ricevere il traffico.

Se si esegue la migrazione di una soluzione basata su cloud Spring esistente ad Azure, assicurarsi che le istanze del server di _configurazione_ e _del registro di sistema del servizio_ ad hoc siano rimosse (o disabilitate) per evitare conflitti con le istanze gestite fornite dal cloud Spring di Azure.

È anche possibile controllare i log del client _del registro di sistema del servizio_ in Azure log Analytics. Per altre informazioni, vedere [analizzare i log e le metriche con le impostazioni di diagnostica](diagnostic-services.md)

Per altre informazioni su Azure Log Analytics, vedere [Introduzione ai log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Eseguire una query sui log usando il [linguaggio di query kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Si vuole esaminare le variabili di ambiente dell'applicazione

Le variabili di ambiente informano il Framework di Azure Spring cloud, assicurando che Azure conosca dove e come configurare i servizi che costituiscono l'applicazione. Assicurare la correttezza delle variabili di ambiente è un primo passaggio necessario nella risoluzione dei potenziali problemi.  È possibile usare l'endpoint di Spring Boot Actuator per esaminare le variabili di ambiente.  

> [!WARNING]
> Questa procedura espone le variabili di ambiente usando l'endpoint di test.  Non continuare se l'endpoint di test è accessibile pubblicamente o se è stato assegnato un nome di dominio all'applicazione.

1. Passare a `https://<your application test endpoint>/actuator/health`.  
    - Una risposta simile a `{"status":"UP"}` indica che l'endpoint è stato abilitato.
    - Se la risposta è negativa, includere la dipendenza seguente nel file *POM. XML* :

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Con l'endpoint dell'attuatore Spring Boot abilitato, passare alla portale di Azure e cercare la pagina di configurazione dell'applicazione.  Aggiungere una variabile di ambiente con il nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` e il valore `*`. 

1. Riavviare l'applicazione.

1. Passare a `https://<your application test endpoint>/actuator/env` e controllare la risposta.  L'aspetto dovrebbe risultare simile al seguente:

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

Cercare il nodo figlio denominato `systemEnvironment`.  Questo nodo contiene le variabili di ambiente dell'applicazione.

> [!IMPORTANT]
> Ricordarsi di annullare l'esposizione delle variabili di ambiente prima di rendere l'applicazione accessibile al pubblico.  Passare alla portale di Azure, cercare la pagina di configurazione dell'applicazione ed eliminare la variabile di ambiente: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Non è possibile trovare metriche o log per l'applicazione

Passare a **gestione app** per assicurarsi che lo stato dell'applicazione sia _in esecuzione_ e _su_.

Se è possibile visualizzare le metriche da _JVM_ ma nessuna metrica da _Tomcat_, verificare se la dipendenza `spring-boot-actuator` è abilitata nel pacchetto dell'applicazione e se è stata avviata correttamente.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se i log dell'applicazione possono essere archiviati in un account di archiviazione ma non inviati ad Azure Log Analytics, verificare se [l'area di lavoro è stata impostata correttamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se si usa un livello gratuito di Azure Log Analytics, si noti che [il livello gratuito non fornisce un contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
