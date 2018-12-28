---
title: Impostazioni del contenitore Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: L'ambiente di runtime del contenitore LUIS si configura mediante gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080266"
---
# <a name="configure-containers"></a>Configurare i contenitori

L'ambiente di runtime del contenitore Language Understanding (LUIS) si configura mediante gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le [impostazioni di montaggio](#mount-settings) di input e le impostazioni di fatturazione. 

Le impostazioni del contenitore sono [gerarchiche](#hierarchical-settings) e possono essere impostate tramite [variabili di ambiente](#environment-variable-settings) o [argomenti della riga di comando](#command-line-argument-settings) di Docker.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No |[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Yes|[Fatturazione](#billing-setting)|Specifica l'URI dell'endpoint della risorsa _Language Understanding_ in Azure.|
|Yes|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No |[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No |[registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|Yes|[Mounts](#mount-settings)|Leggere e scrivere dati dal [computer host](luis-container-howto.md#the-host-computer) al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](luis-container-howto.md#billing) (Fatturazione).

## <a name="apikey-setting"></a>Impostazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave di configurazione valida per la risorsa _Language Understanding_ specificata per l'impostazione di configurazione [`Billing`](#billing-setting).

Questa impostazione è disponibile in due posizioni:

* Portale di Azure: **Language Understanding's** Resource Management, sotto **Keys** (Chiavi)
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**. 

Non usare il tasto starter o la chiave di creazione. 

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

L'impostazione `ApplicationInsights` consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Application Insights offre funzionalità di monitoraggio avanzate del contenitore. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|No | `InstrumentationKey` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Esempio:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Impostazione di fatturazione

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Language Understanding_ in Azure utilizzata per misurare i dati di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere un URI dell'endpoint valido per una risorsa _Language Understanding_ in Azure.

Questa impostazione è disponibile in due posizioni:

* Portale di Azure: Panoramica di **Language Understanding**, con l'etichetta `Endpoint`
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**, come parte dell'URI dell'endpoint.

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Yes| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Impostazione Eula

L'impostazione `Eula` indica che è stata accettata la licenza per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere impostato su `accept`.

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Yes| `Eula` | string | Accettazione della licenza<br><br>Esempio:<br>`Eula=accept` |

I contenitori di Servizi cognitivi sono concessi in licenza in base al [contratto](https://go.microsoft.com/fwlink/?linkid=2018657) che disciplina l'uso di Azure. Se non si dispone di tale contratto, si acconsente che l'uso di Azure sia disciplinato dal [Contratto di Sottoscrizione Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), in cui sono incluse le [condizioni per l'uso dei Servizi Online](https://go.microsoft.com/fwlink/?linkid=2018760). Per le anteprime si accettano inoltre le [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Con l'uso del contenitore si acconsente a rispettare tali condizioni.

## <a name="fluentd-settings"></a>Impostazioni Fluentd

Fluentd è un agente di raccolta dati open source per la registrazione unificata. Le impostazioni `Fluentd` gestiscono la connessione del contenitore a un server [Fluentd](https://www.fluentd.org). Il contenitore LUIS include un provider di registrazione di Fluentd che consente al contenitore di scrivere log e, facoltativamente, dati delle metriche in un server di Fluentd.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Fluentd`.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Host` | string | Indirizzo IP o nome host DNS del server Fluentd. |
| `Port` | Integer | Porta del server Fluentd.<br/> Il valore predefinito è 24224. |
| `HeartbeatMs` | Integer | Intervallo di heartbeat, espresso in millisecondi. Se prima della scadenza di questo intervallo è non stato inviato alcun traffico dell'evento, viene inviato un heartbeat al server Fluentd. Il valore predefinito è 60000 millisecondi (1 minuto). |
| `SendBufferSize` | Integer | Spazio di buffer di rete, espresso in byte, allocato per le operazioni di invio. Il valore predefinito è 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, espresso in millisecondi, per stabilire una connessione SSL/TLS con il server Fluentd. Il valore predefinito è 10000 millisecondi (10 secondi).<br/> Se `UseTLS` è impostato su false, questo valore viene ignorato. |
| `UseTLS` | boolean | Indica se il contenitore deve usare SSL/TLS per comunicare con il server Fluentd. Il valore predefinito è False. |

## <a name="logging-settings"></a>Impostazioni di registrazione

Le impostazioni `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni di configurazione e gli stessi valori per il contenitore che si usano per un'applicazione ASP.NET Core. 

I provider di registrazione seguenti sono supportati dal contenitore LUIS:

|Provider|Scopo|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Provider di registrazione `Console` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Eseguire il debug](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Provider di registrazione `Debug` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Disco](#disk-logging)|Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.|

### <a name="disk-logging"></a>Registrazione su disco
  
Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:  

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Format` | string | Formato di output dei file di log.<br/> **Nota:** Per abilitare il provider di registrazione, questo valore deve essere impostato su `json`. Se questo valore viene specificato senza specificare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
| `MaxFileSize` | Integer | Dimensione massima, espressa in megabyte (MB), di un file di log. Quando la dimensione del file di log corrente corrisponde a questo valore o lo supera, il provider di registrazione avvia un nuovo file di log. Se viene specificato -1, la dimensione del file di log è limitata solo dalla dimensione massima del file del montaggio di output eventualmente presente. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione del supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/). 

Il contenitore LUIS non usa montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](luis-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

La tabella seguente descrive le impostazioni supportate.

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Yes| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`. Questo è il percorso del file del pacchetto LUIS. <br><br>Esempio:<br>`--mount type=bind,src=c:\input,target=/input`|
|No | `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log di query LUIS e i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Impostazioni gerarchiche

Le impostazioni per il contenitore di LUIS sono gerarchiche e tutti i contenitori nel [computer host](luis-container-howto.md#the-host-computer) usano una gerarchia condivisa.

Per specificare le impostazioni è possibile usare una delle tecniche seguenti:

* [Variabili di ambiente](#environment-variable-settings)
* [Argomenti della riga di comando](#command-line-argument-settings)

I valori delle variabili di ambiente sostituiscono i valori degli argomenti della riga di comando, che a loro volta sostituiscono i valori predefiniti per l'immagine del contenitore. Se si specificano valori diversi in una variabile di ambiente e in un argomento della riga di comando per la stessa impostazione di configurazione per il contenitore di cui è stata creata l'istanza viene usato il valore nella variabile di ambiente.

|Precedenza|Impostazione della posizione|
|--|--|
|1|Variabile di ambiente| 
|2|Riga di comando|
|3|Valore predefinito dell'immagine del contenitore|

### <a name="environment-variable-settings"></a>Impostazioni della variabile di ambiente

I vantaggi dell'uso delle variabili di ambiente sono:

* È possibile configurare più impostazioni.
* Più contenitori possono usare le stesse impostazioni.

### <a name="command-line-argument-settings"></a>Impostazioni degli argomenti della riga di comando

Il vantaggio di usare gli argomenti della riga di comando è che ogni contenitore può usare impostazioni diverse.

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](luis-container-howto.md#stop-the-container).


* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori docker.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Valore | Formato o esempio |
|-------------|-------|---|
|{ENDPOINT_KEY} | La chiave di endpoint dell'applicazione LUIS di cui si esegue il training. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Il valore dell'endpoint billing (fatturazione) è disponibile nella pagina della panoramica di Language Understanding del portale di Azure.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore e avviarlo; altrimenti il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](luis-container-howto.md#billing).
> Il valore ApiKey è la **chiave** della pagina delle chiavi ed endpoint nel portale di LUIS ed è anche disponibile nella pagina delle chiavi di Azure Language Understanding Resource. 

### <a name="basic-example"></a>Esempio di base

L'esempio seguente include gli argomenti minimi possibili per eseguire il contenitore:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Il comando precedente usa la directory dell'unità `c:` per evitare conflitti di autorizzazione in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio docker. Il comando docker precedente usa la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del [computer host](luis-container-howto.md#the-host-computer) del sistema operativo. Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori docker.


### <a name="applicationinsights-example"></a>Esempio di ApplicationInsights

L'esempio seguente imposta l'argomento ApplicationInsights per inviare dati di telemetria ad Application Insights mentre il contenitore è in esecuzione:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Esempio di registrazione con argomenti della riga di comando

Il comando seguente imposta il livello di registrazione, `Logging:Console:LogLevel`, per configurare il livello di registrazione su [`Information`](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Esempio di registrazione con variabile di ambiente

I comandi seguenti usano una variabile di ambiente denominata `Logging:Console:LogLevel` per configurare il livello di registrazione su [`Information`](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](luis-container-howto.md)
* Per risolvere i problemi correlati alla funzionalità LUIS, fare riferimento alle [domande frequenti](luis-resources-faq.md).