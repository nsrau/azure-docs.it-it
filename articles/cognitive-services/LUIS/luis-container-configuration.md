---
title: Impostazioni del contenitore Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: L'ambiente di runtime del contenitore LUIS si configura mediante gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: e05998f74223ead6bb4e94b86469e51791e0263f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678565"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurare i contenitori Docker di Language Understanding 

L'ambiente di runtime del contenitore **Language Understanding** (LUIS) viene configurato tramite gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le [impostazioni di montaggio](#mount-settings) di input e le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatorio|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey](#apikey-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No |[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Fatturazione](#billing-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No |[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No |[Proxy HTTP](#http-proxy-credentials-settings)|Configurare un proxy HTTP per le richieste in uscita.|
|No |[registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|Sì|[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](luis-container-howto.md#billing) (Fatturazione).

## <a name="apikey-setting"></a>Impostazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per la chiave API e il valore deve essere una chiave valida per il _servizi cognitivi_ risorsa specificata per il [ `Billing` ](#billing-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** gestione delle risorse, in **chiavi**
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**. 

Non usare il tasto starter o la chiave di creazione. 

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Impostazione di fatturazione

Il `Billing` impostazione specifica l'URI dell'endpoint del _servizi cognitivi_ risorsa di Azure usato per controllare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI dell'endpoint valido per un _servizi cognitivi_ risorse in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** panoramica, con l'etichetta `Endpoint`
* Portale LUIS: Pagina delle **impostazioni di chiavi ed endpoint**, come parte dell'URI dell'endpoint.

Ricordare di includere il `luis/v2.0` routing nell'URL come illustrato nella tabella seguente:


|Obbligatorio| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Sì| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/). 

Il contenitore LUIS non usa montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](luis-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

La tabella seguente descrive le impostazioni supportate.

|Obbligatorio| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Sì| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`. Questo è il percorso del file del pacchetto LUIS. <br><br>Esempio:<br>`--mount type=bind,src=c:\input,target=/input`|
|No | `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log di query LUIS e i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](luis-container-howto.md#stop-the-container).


* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Ricordare di includere il `luis/v2.0` routing nell'URL come illustrato nella tabella seguente.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Valore | Formato o esempio |
|-------------|-------|---|
|{ENDPOINT_KEY} | La chiave di endpoint dell'applicazione LUIS di cui si esegue il training. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Il valore dell'endpoint fatturazione è disponibile in Azure `Cognitive Services` pagina Panoramica. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](luis-container-howto.md#billing).
> Il valore ApiKey è il **Key** le chiavi e gli endpoint di pagina nel portale di LUIS ed è anche disponibile in Azure `Cognitive Services` pagina chiavi di risorsa. 

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

### <a name="logging-example"></a>Esempio di registrazione 

Il comando seguente imposta il livello di registrazione, `Logging:Console:LogLevel`, per configurare il livello di registrazione su [`Information`](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](luis-container-howto.md)
* Per risolvere i problemi correlati alle funzionalità di LUIS, vedere [Risoluzione dei problemi](troubleshooting.md).
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
