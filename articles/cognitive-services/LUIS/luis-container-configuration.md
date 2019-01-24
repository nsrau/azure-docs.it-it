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
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: e8e838fae0da3a47fe1b3ec8d412f956f5f28034
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975510"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurare i contenitori docker di Language Understanding 

L'ambiente di runtime del contenitore Language Understanding (LUIS) si configura mediante gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le [impostazioni di montaggio](#mount-settings) di input e le impostazioni di fatturazione. 

Le impostazioni del contenitore sono [gerarchiche](#hierarchical-settings) e possono essere impostate tramite [variabili di ambiente](#environment-variable-settings) o [argomenti della riga di comando](#command-line-argument-settings) di Docker.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No |[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Yes|[Billing](#billing-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Yes|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No |[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No |[Logging](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|Yes|[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](luis-container-howto.md#billing) (Fatturazione).

## <a name="apikey-setting"></a>Impostazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave di configurazione valida per la risorsa _Language Understanding_ specificata per l'impostazione di configurazione [`Billing`](#billing-setting).

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Language Understanding's** Resource Management, sotto **Keys** (Chiavi)
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**. 

Non usare il tasto starter o la chiave di creazione. 

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Impostazione di fatturazione

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Language Understanding_ in Azure utilizzata per misurare i dati di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere un URI dell'endpoint valido per una risorsa _Language Understanding_ in Azure.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica di **Language Understanding**, con l'etichetta `Endpoint`
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**, come parte dell'URI dell'endpoint.

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Yes| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


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

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


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
> Il comando precedente usa la directory dell'unità `c:` per evitare conflitti di autorizzazione in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker. Il comando Docker precedente usa la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituire o rimuovere questo carattere in base ai requisiti del sistema operativo del [computer host](luis-container-howto.md#the-host-computer). Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori docker.


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
