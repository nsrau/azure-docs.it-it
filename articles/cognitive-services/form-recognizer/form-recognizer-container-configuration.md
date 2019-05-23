---
title: Configurare il contenitore - Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare il contenitore Riconoscimento modulo per analizzare i dati di moduli e tabelle.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/15/2019
ms.author: pafarley
ms.openlocfilehash: 17cf1d88701370c4f81eab4f0d2df33ee2e94af5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796409"
---
# <a name="configure-form-recognizer-containers"></a>Configurare i contenitori di Riconoscimento modulo

I contenitori di Riconoscimento modulo permettono ai clienti di creare un'architettura per le applicazioni ottimizzata per sfruttare i vantaggi delle funzionalità cloud avanzate e della posizione fisica dei dispositivi perimetrali.

Per configurare l'ambiente di runtime del contenitore **Riconoscimento modulo**, si usano gli argomenti del comando `docker run`. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](form-recognizer-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave valida per la risorsa _Riconoscimento modulo_ specificata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Gestione risorse di **Riconoscimento moduloartificiale**, in **Chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Riconoscimento modulo_ in Azure usata per misurare i dati di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI dell'endpoint valido per una risorsa _Riconoscimento modulo_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica di **Riconoscimento modulo**, con etichetta `Endpoint`

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Sì| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

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

Con il contenitore di Riconoscimento modulo è richiesto un montaggio di input e un montaggio di output. Il montaggio di input può essere di sola lettura ed è necessario per accedere ai dati che verranno usati per il training e l'assegnazione del punteggio. Il montaggio di output deve essere accessibile in scrittura e verrà usato per archiviare i modelli e i dati temporanei.

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](form-recognizer-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host.

|Facoltativo| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Obbligatoria| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`.    <br><br>Esempio:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obbligatoria| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`.  <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](form-recognizer-container-howto.md#stop-the-container).

* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host.
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi di Riconoscimento modulo del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina della panoramica di Riconoscimento modulo del portale di Azure.|
|{COMPUTER_VISION_API_KEY}| La chiave è disponibile nella pagina Chiavi dell'API di Visione artificiale del portale di Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|L'endpoint di fatturazione. Se si usa una risorsa Visione artificiale basata sul cloud, il valore dell'URI è disponibile nella pagina Panoramica dell'API di Visione artificiale del portale di Azure. Se si usa un contenitore `cognitive-services-recognize-text`, usare l'URL dell'endpoint di fatturazione passato al contenitore nel comando `docker run`.|

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing-configuration-setting).
> Il valore ApiKey è la **chiave** derivante dalla pagina delle chiavi della risorsa Riconoscimento modulo di Azure.

## <a name="form-recognizer-container-docker-examples"></a>Esempi relativi ai contenitori di Riconoscimento modulo in Docker

Gli esempi di Docker seguenti si riferiscono al contenitore Riconoscimento modulo.

### <a name="basic-example-for-form-recognizer"></a>Esempio di base per Riconoscimento modulo

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Esempio di registrazione per Riconoscimento modulo

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](form-recognizer-container-howto.md)
