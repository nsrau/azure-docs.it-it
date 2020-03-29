---
title: Guida alla risoluzione dei problemi di Azure Spring Cloud | Microsoft Docs
description: Guida alla risoluzione dei problemi di Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277574"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Risolvere i problemi comuni di Azure Spring CloudTroubleshoot common Azure Spring Cloud issues

Questo articolo fornisce istruzioni per la risoluzione dei problemi di sviluppo di Azure Spring Cloud.This article provides instructions for troubleshooting Azure Spring Cloud development issues. Per altre informazioni, vedere Domande frequenti su [Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemi di disponibilità, prestazioni e applicazione

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Impossibile avviare l'applicazione (ad esempio, l'endpoint non può essere connesso o restituisce un 502 dopo alcuni tentativi)My application can't start (for example, the endpoint can't be connected or it returns a 502 after a retries)

Esportare i log in Azure Log Analytics. La tabella per i registri dell'applicazione Spring è denominata *AppPlatformLogsforSpring*. Per altre informazioni, vedere [Analizzare log e metriche con le impostazioni](diagnostic-services.md)di diagnostica .

Nei registri potrebbe essere visualizzato il seguente messaggio di errore:

> "org.springframework.context.ApplicationContextException: Impossibile avviare il server web"

Il messaggio indica uno dei due probabili problemi: 
* Uno dei bean o una delle relative dipendenze risulta mancante.
* Una delle proprietà bean risulta mancante o non valida. In questo caso, è probabile che venga visualizzato "java.lang.IllegalArgumentException".

Le associazioni di servizio possono anche causare errori di avvio dell'applicazione. Per eseguire query sui log, utilizzare parole chiave correlate ai servizi associati. Ad esempio, si supponga che l'applicazione dispone di un'associazione a un MySQL istanza che è impostata sull'ora di sistema locale. Se l'applicazione non viene avviata, nel registro potrebbe essere visualizzato il seguente messaggio di errore:

> "java.sql.SQLException: il valore del fuso orario del server 'Coordinated Universal Time' non è riconosciuto o rappresenta più di un fuso orario."

Per correggere l'errore, `server parameters` passare all'istanza di `time_zone` MySQL e modificare il valore da *SYSTEM* a *.*


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>L'applicazione si arresta in modo anomalo o presenta un errore imprevisto

Quando si esegue il debug dell'applicazione si blocca, avviare controllando lo stato di esecuzione e lo stato di individuazione dell'applicazione. A tale scopo, passare a _Gestione app_ nel portale di Azure per verificare che gli stati di tutte le applicazioni siano _In esecuzione_ e _UP_.

* Se lo stato è _In esecuzione_ ma lo stato di individuazione non è _UP,_ passare alla sezione ["Impossibile registrare l'applicazione".](#my-application-cant-be-registered)

* Se lo stato di individuazione è _UP_, passare a Metriche per verificare l'integrità dell'applicazione. Esaminare le metriche seguenti:


  - `TomcatErrorCount`(_tomcat.global.error_): tutte le eccezioni dell'applicazione Spring vengono conteggiate qui. Se questo numero è elevato, passare ad Azure Log Analytics per esaminare i log dell'applicazione.

  - `AppMemoryMax`(_jvm.memory.max_): la quantità massima di memoria disponibile per l'applicazione. L'importo potrebbe essere indefinito o potrebbe cambiare nel tempo se è definito. Se è definito, la quantità di memoria utilizzata e di cui è stato eseguito il commit è sempre minore o uguale a max. Tuttavia, un'allocazione `OutOfMemoryError` di memoria potrebbe non riuscire con un messaggio se l'allocazione tenta di aumentare la memoria utilizzata in modo che *utilizzava > di cui è stato eseguito il commit*, anche se *utilizzata <, max* è ancora true. In tal caso, provare ad aumentare la `-Xmx` dimensione massima dell'heap utilizzando il parametro .

  - `AppMemoryUsed`(_jvm.memory.used_): la quantità di memoria in byte attualmente utilizzata dall'applicazione. Per un'applicazione Java con carico normale, questa serie metrica forma un modello a dente di *sega,* in cui l'utilizzo della memoria aumenta e diminuisce costantemente in piccoli incrementi e scende improvvisamente molto, quindi il modello si ripete. Questa serie metrica si verifica a causa della procedura di Garbage Collection all'interno della macchina virtuale Java, in cui le azioni di raccolta rappresentano cadute sul modello a dente di sega.
    
    Questa metrica è importante per identificare i problemi di memoria, ad esempio:This metric is important to help identify memory issues, such as:
    * Un'esplosione di memoria all'inizio.
    * Allocazione di memoria di impennata per un percorso logico specifico.
    * Perdite di memoria graduali.

  Per ulteriori informazioni, vedere [Metriche](spring-cloud-concept-metrics.md).

Per altre informazioni su Azure Log Analytics, vedere Introduzione a Log Analytics in Monitoraggio di Azure.To learn more about Azure Log Analytics, see [Get started with Log Analytics in Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Nell'applicazione si verifica un uso elevato della CPU o un uso elevato della memoria

Se l'applicazione riscontra un utilizzo elevato della CPU o della memoria, è vero:
* Tutte le istanze dell'app riscontrano un utilizzo elevato della CPU o della memoria.
* Alcune istanze dell'app riscontrano un utilizzo elevato della CPU o della memoria.

Per verificare quale situazione si applica, procedere come segue:

1. Passare a **Metriche**, quindi selezionare **Percentuale utilizzo CPU del servizio** o Memoria del servizio **utilizzata**.
2. Aggiungere un filtro **App** per specificare l'applicazione che si desidera monitorare.
3. Dividere le metriche per **Istanza**.

Se *tutte le istanze* sono in grado di utilizzare una CPU o una memoria elevata, è necessario scalare orizzontalmente l'applicazione o aumentare l'utilizzo della CPU o della memoria. Per altre informazioni, vedere [Esercitazione: Ridimensionare un'applicazione in Azure Spring Cloud.](spring-cloud-tutorial-scale-manual.md)

Se *alcune istanze* hanno un utilizzo elevato della CPU o della memoria, controllare lo stato dell'istanza e il relativo stato di individuazione.

Per altre informazioni, vedere [Metriche per Azure Spring Cloud.For](spring-cloud-concept-metrics.md)more information, see Metrics for Azure Spring Cloud .

Se tutte le istanze sono in esecuzione, passare ad Azure Log Analytics per eseguire query sui log dell'applicazione ed esaminare la logica del codice. Questo ti aiuterà a vedere se qualcuno di essi potrebbe influenzare il partizionamento della bilancia. Per ulteriori informazioni, consultate [Analizzare log e metriche con le impostazioni](diagnostic-services.md)di diagnostica.

Per altre informazioni su Azure Log Analytics, vedere Introduzione a Log Analytics in Monitoraggio di Azure.To learn more about Azure Log Analytics, see [Get started with Log Analytics in Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) Eseguire una query sui log utilizzando il linguaggio di [query Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Elenco di controllo per la distribuzione dell'applicazione Spring in Azure Spring Cloud

Prima di utilizzare l'applicazione, assicurarsi che soddisfi i seguenti criteri:

* L'applicazione può essere eseguita localmente con la versione del runtime Java specificata.
* La configurazione dell'ambiente (CPU/RAM/istanze) soddisfa i requisiti minimi definiti dal provider di applicazioni.
* Gli elementi di configurazione hanno i valori previsti. Per altre informazioni, vedere [Config Server](spring-cloud-tutorial-config-server.md).
* Le variabili di ambiente hanno i valori previsti.
* I parametri JVM hanno i valori previsti.
* Si consiglia di disattivare o rimuovere i servizi _Config Server_ e Spring _Service Registry_ incorporati dal pacchetto dell'applicazione.
* Se è necessario associare risorse di Azure tramite l'_associazione al servizio_, verificare che le risorse di destinazione siano in esecuzione.

## <a name="configuration-and-management"></a>Configurazione e gestione

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Si è verificato un problema con la creazione di un'istanza del servizio Azure Spring Cloud

Quando si configura un'istanza del servizio Cloud di Azure Spring tramite il portale di Azure, Azure Spring Cloud esegue la convalida per l'utente.

Tuttavia, se si tenta di configurare l'istanza del servizio Cloud di Azure Spring usando [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) o il modello di Azure Resource [Manager,](https://docs.microsoft.com/azure/azure-resource-manager/)verificare che:But if you try to set up the Azure Spring Cloud instance by using the Azure CLI or the Azure Resource Manager template , verify that:

* La sottoscrizione è attiva.
* La posizione è [supportata](spring-cloud-faq.md) da Azure Spring Cloud.The location is supported by Azure Spring Cloud.
* Il gruppo di risorse per l'istanza è già stato creato.
* Il nome della risorsa è conforme alla regola di denominazione. Deve contenere solo lettere minuscole, numeri e trattini. Il primo carattere deve essere una lettera. L'ultimo carattere deve essere una lettera o un numero. Il valore deve contenere da 2 a 32 caratteri.

Se si vuole configurare l'istanza del servizio Cloud di Azure Spring usando il modello Resource Manager, fare riferimento innanzitutto a Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Il nome dell'istanza del servizio Azure Spring Cloud verrà `azureapps.io`usato per richiedere un nome di sottodominio in , pertanto l'installazione avrà esito negativo se il nome è in conflitto con uno esistente. È possibile trovare ulteriori dettagli nei registri attività.

### <a name="i-cant-deploy-a-jar-package"></a>Non è possibile distribuire un pacchetto JAR

Non è possibile caricare il file di archivio Java (JAR)/pacchetto di origine usando il portale di Azure o il modello di Resource Manager.You can't upload Java Archive file (JAR)/source package by using the Azure portal or the Resource Manager template.

Quando si distribuisce il pacchetto dell'applicazione usando l'interfaccia della riga di comando di [Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)l'interfaccia della riga di comando di Azure esegue periodicamente il polling dello stato di avanzamento della distribuzione e, alla fine, visualizza il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Assicurarsi che l'applicazione sia inclusa nel pacchetto nel [formato JAR eseguibile](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)corretto. Se il pacchetto non è stato incluso correttamente, verrà visualizzato un messaggio di errore analogo al seguente:

> "Errore: file jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Non è possibile distribuire un pacchetto di origine

Non è possibile caricare il pacchetto JAR/source usando il portale di Azure o il modello di Resource Manager.You can't upload JAR/source package by using the Azure portal or the Resource Manager template.

Quando si distribuisce il pacchetto dell'applicazione usando l'interfaccia della riga di comando di [Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)l'interfaccia della riga di comando di Azure esegue periodicamente il polling dello stato di avanzamento della distribuzione e, alla fine, visualizza il risultato della distribuzione.

Se il polling viene interrotto, è comunque possibile usare il comando seguente per recuperare i log di progettazione e distribuzione:

`az spring-cloud app show-deploy-log -n <app-name>`

Si noti tuttavia che un'istanza del servizio Cloud di Azure Può attivare un solo processo di compilazione per un pacchetto di origine alla volta. Per altre informazioni, vedere [Distribuire un'applicazione](spring-cloud-quickstart-launch-app-portal.md) e Configurare un ambiente di gestione temporanea in Azure Spring Cloud.For more information, see Deploy an application and [Set up a staging environment in Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>La mia domanda non può essere registrata

Nella maggior parte dei casi, questa situazione si verifica quando *le dipendenze necessarie* e *l'individuazione* del servizio non sono configurati correttamente nel file del modello a oggetti di progetto (POM). Una volta configurato, l'endpoint del server del Registro di sistema del servizio incorporato viene inserito come variabile di ambiente con l'applicazione. Le applicazioni si registrano quindi con il server del Registro di sistema del servizio e individuano altri microservizi dipendenti.

Attendere almeno due minuti prima che un'istanza appena registrata inizi a ricevere traffico.

Se si esegue la migrazione di una soluzione basata su Spring Cloud esistente in Azure, assicurarsi che le istanze del _Registro_ di sistema del servizio ad hoc e del server di configurazione siano state rimosse (o disabilitate) per evitare conflitti con le istanze gestite fornite da Azure Spring Cloud.If you're migrating an existing Spring Cloud-based solution to Azure, ensure that your ad-hoc Service Registry and _Config Server_ instances are removed (or disabled) to avoid conflicting with the managed instances provided by Azure Spring Cloud.

È anche possibile controllare i log del client _del Registro di sistema del servizio_ in Azure Log Analytics.You can also check the Service Registry client logs in Azure Log Analytics. Per altre informazioni, vedere [Analizzare log e metriche con le impostazioni](diagnostic-services.md) di diagnosticaFor more information, see Analyze logs and metrics with diagnostics settings

Per altre informazioni su Azure Log Analytics, vedere Introduzione a Log Analytics in Monitoraggio di Azure.To learn more about Azure Log Analytics, see [Get started with Log Analytics in Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) Eseguire una query sui log utilizzando il linguaggio di [query Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Si vuole esaminare le variabili di ambiente dell'applicazione

Le variabili di ambiente informano il framework di Azure Spring Cloud, assicurando che Azure comprenda dove e come configurare i servizi che costituiscono l'applicazione. Assicurare la correttezza delle variabili di ambiente è un primo passaggio necessario nella risoluzione dei potenziali problemi.  È possibile usare l'endpoint di Spring Boot Actuator per esaminare le variabili di ambiente.  

> [!WARNING]
> Questa procedura espone le variabili di ambiente usando l'endpoint di test.  Non continuare se l'endpoint di test è accessibile pubblicamente o se è stato assegnato un nome di dominio all'applicazione.

1. Passare a `https://<your application test endpoint>/actuator/health`.  
    - Una risposta simile a `{"status":"UP"}` indica che l'endpoint è stato abilitato.
    - Se la risposta è negativa, includere la dipendenza seguente nel file *POM.xml:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Con l'endpoint Discarica prima linea abilitata, passare al portale di Azure e cercare la pagina di configurazione dell'applicazione.  Aggiungere una variabile di `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` ambiente `*` con il nome e il valore . 

1. Riavviare l'applicazione.

1. Vai `https://<your application test endpoint>/actuator/env` a e ispezionare la risposta.  L'aspetto dovrebbe risultare simile al seguente:

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

Cercare il nodo `systemEnvironment`figlio denominato .  Questo nodo contiene le variabili di ambiente dell'applicazione.

> [!IMPORTANT]
> Ricordarsi di annullare l'esposizione delle variabili di ambiente prima di rendere l'applicazione accessibile al pubblico.  Passare al portale di Azure, cercare la pagina di configurazione `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`dell'applicazione ed eliminare questa variabile di ambiente: . .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Non riesco a trovare metriche o log per la mia applicazione

Passare a **Gestione app** per verificare che gli stati dell'applicazione siano _In esecuzione_ e _UP_.

Se è possibile visualizzare le metriche da _JVM_ ma nessuna metrica da _Tomcat_, verificare se la `spring-boot-actuator` dipendenza è abilitata nel pacchetto dell'applicazione e che viene avviato correttamente.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se i log dell'applicazione possono essere archiviati in un account di archiviazione ma non inviati ad Azure Log Analytics, verificare se l'area di lavoro è [stata configurata correttamente.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Se si usa un livello gratuito di Azure Log Analytics, tenere presente che [il livello gratuito non fornisce un contratto](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)di servizio.
