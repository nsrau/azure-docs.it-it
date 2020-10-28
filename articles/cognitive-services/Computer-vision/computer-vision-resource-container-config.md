---
title: Configurare i contenitori OCR di lettura-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come configurare le impostazioni obbligatorie e facoltative per i contenitori OCR di lettura in Visione artificiale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5094bd4aa5ac68c24f284cfb74e410fbdf089af7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677180"
---
# <a name="configure-read-ocr-docker-containers"></a>Configurare i contenitori Docker di lettura OCR

È possibile configurare l'ambiente di runtime del contenitore OCR di Visione artificiale Read usando gli `docker run` argomenti del comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) Impostazioni, e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutti e tre gli elementi; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](computer-vision-how-to-install-containers.md) (Fatturazione).

Il contenitore include anche le seguenti impostazioni di configurazione specifiche del contenitore:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|No|ReadEngineConfig:ResultExpirationPeriod| solo contenitori v 2.0. Periodo di scadenza del risultato in ore. L'impostazione predefinita è 48 ore. L'impostazione specifica quando il sistema deve cancellare i risultati del riconoscimento. Se, ad esempio `resultExpirationPeriod=1` , il sistema cancella il risultato del riconoscimento 1 ora dopo il processo. Se `resultExpirationPeriod=0` , il sistema cancella il risultato del riconoscimento dopo che il risultato è stato recuperato.|
|No|Cache: Redis| solo contenitori v 2.0. Abilita l'archiviazione Redis per l'archiviazione dei risultati. È *necessaria* una cache se più contenitori di lettura sono posizionati dietro un servizio di bilanciamento del carico.|
|No|Coda: RabbitMQ|solo contenitori v 2.0. Abilita RabbitMQ per l'invio di attività. Questa impostazione è utile quando più contenitori di lettura sono posizionati dietro un servizio di bilanciamento del carico.|
|No|Coda: Azure: QueueVisibilityTimeoutInMilliseconds | solo contenitori V3. x. Tempo di invisibilità di un messaggio quando un altro thread di lavoro lo elabora. |
|No|Archiviazione::D ocumentStore:: MongoDB|solo contenitori v 2.0. Abilita MongoDB per l'archiviazione dei risultati permanenti. |
|No|Archiviazione: ObjectStore: AzureBlob: ConnectionString| solo contenitori V3. x. Stringa di connessione dell'archiviazione BLOB di Azure. |

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L' `ApiKey` impostazione specifica la `Cognitive Services` chiave di risorsa di Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave valida per la risorsa _Servizi cognitivi_ specificata per l' [`Billing`](#billing-configuration-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: gestione delle risorse di **Servizi cognitivi** , in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L' `Billing` impostazione specifica l'URI dell'endpoint della risorsa _Servizi cognitivi_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa di _Servizi cognitivi_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica di **Servizi cognitivi** , con etichetta `Endpoint`

Ricordarsi di aggiungere il `vision/v1.0` routing all'URI dell'endpoint, come illustrato nella tabella seguente. 

|Obbligatoria| Nome | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

I contenitori Visione artificiale non usano montaggi di input o di output per l'archiviazione di dati di servizio o di training. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](computer-vision-how-to-install-containers.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| Nome | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | string | I contenitori di Visione artificiale non la usano.|
|Facoltativo| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](computer-vision-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga** : i comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\` , come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine** degli argomenti: non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

Sostituire { _nome_argomento_ } con i propri valori:

| Segnaposto | Valore | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave dell'endpoint della `Computer Vision` risorsa nella `Computer Vision` pagina chiavi di Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Il valore dell'endpoint di fatturazione è disponibile nella `Computer Vision` pagina Panoramica di Azure.| Vedere [raccolta di parametri obbligatori](computer-vision-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](computer-vision-how-to-install-containers.md#billing).
> Il valore ApiKey è la **chiave** della `Cognitive Services` pagina chiavi di risorsa di Azure.

## <a name="container-docker-examples"></a>Esempi di contenitori Docker

Gli esempi di Docker seguenti sono per il contenitore di lettura.


# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

### <a name="basic-example"></a>Esempio di base

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Esempio di registrazione 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

### <a name="basic-example"></a>Esempio di base

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Esempio di registrazione 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come installare ed eseguire i contenitori](computer-vision-how-to-install-containers.md).
