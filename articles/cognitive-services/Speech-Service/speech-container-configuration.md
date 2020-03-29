---
title: Configurare i contenitori di riconoscimento vocaleConfigure Speech
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento vocale fornisce a ogni contenitore un framework di configurazione comune, in modo che sia possibile configurare e gestire facilmente l'archiviazione, la registrazione e la telemetria e le impostazioni di sicurezza per i contenitori.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037319"
---
# <a name="configure-speech-service-containers"></a>Configurare i contenitori del servizio di riconoscimento vocale

I contenitori vocali consentono ai clienti di creare un'architettura di applicazione vocale ottimizzata per sfruttare sia le funzionalità cloud affidabili che la località perimetrale. I quattro contenitori di linguaggio che supportiamo ora sono, **sintesi**vocale, **sintesi vocale personalizzato,** **sintesi vocale**e **sintesi vocale**.

L'ambiente di runtime del `docker run` contenitore **Speech** viene configurato utilizzando gli argomenti del comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)impostazioni [`Eula`](#eula-setting) , e vengono utilizzate insieme ed è necessario fornire valori validi per tutti e tre; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](speech-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve [`Billing`](#billing-configuration-setting) essere una chiave valida per la risorsa _di riconoscimento vocale_ specificata per l'impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

- Portale di Azure: Gestione delle risorse **del riconoscimento vocale,** in **ChiaviAzure** portal: Speech's Resource Management, under Keys

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _di riconoscimento vocale_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa _di riconoscimento vocale_ in Azure.You must specify a value for this configuration setting, and the value must be a valid endpoint URI for a Speech resource on Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

- Portale di Azure: Panoramica **del riconoscimento vocale,** etichettatoAzure portal: Speech's Overview, labeled`Endpoint`

| Obbligatoria | Nome | Tipo di dati | Descrizione |
| -------- | ---- | --------- | ----------- |
| Sì | `Billing` | string | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [Raccolta dei parametri obbligatori](speech-container-howto.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

I contenitori di riconoscimento vocale standard non usano supporti di input o output per archiviare i dati di training o del servizio. Tuttavia, i contenitori vocali personalizzati si basano sui supporti di volume.

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](speech-container-howto.md#the-host-computer)potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host.

| Facoltativo | Nome | Tipo di dati | Descrizione |
| -------- | ---- | --------- | ----------- |
| Non consentito | `Input` | string | I contenitori di riconoscimento vocale standard non utilizzano questa opzione. I contenitori vocali personalizzati [utilizzano i supporti](#volume-mount-settings)di volume .                                                                                    |
| Facoltativo | `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Impostazioni di montaggio del volume

I contenitori vocali personalizzati usano i supporti di volume per rendere [persistenti i](https://docs.docker.com/storage/volumes/) modelli personalizzati. È possibile specificare un `-v` montaggio `--volume`del volume aggiungendo l'opzione (o ) al comando [docker run.](https://docs.docker.com/engine/reference/commandline/run/)

I modelli personalizzati vengono scaricati la prima volta che un nuovo modello viene ingerito come parte del comando di esecuzione della finestra mobile del contenitore vocale personalizzato. Le esecuzioni `ModelId` sequenziali dello stesso per un contenitore vocale personalizzato utilizzeranno il modello scaricato in precedenza. Se il montaggio del volume non viene fornito, i modelli personalizzati non possono essere resi persistenti.

L'impostazione di montaggio del volume è costituita da tre campi separati da colore: `:`

1. Il primo campo è il nome del volume sul computer host, ad esempio _C:._
2. Il secondo campo è la directory nel contenitore, ad esempio _/usr/local/models_.
3. Il terzo campo (facoltativo) è un elenco di opzioni separate da virgole, per ulteriori informazioni vedere [utilizzare volumes](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Esempio di montaggio del volume

```bash
-v C:\input:/usr/local/models
```

Questo comando consente di montare la directory _C:/input_ del computer host nella directory _/usr/local/models._

> [!IMPORTANT]
> Le impostazioni di montaggio del volume sono applicabili solo ai contenitori **di sintesi** vocale personalizzati e di sintesi **vocale personalizzati.** I contenitori standard **di sintesi** vocale e **sintesi vocale** non utilizzano supporti di volume.

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`. Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](speech-container-howto.md#stop-the-container).

- **Carattere di continuazione di**riga: i comandi Docker nelle sezioni seguenti utilizzano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host.
- **Ordine degli argomenti**: Non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | valore | Formato o esempio |
| ----------- | ----- | ----------------- |
| **"API_KEY"** | Chiave dell'endpoint `Speech` della risorsa `Speech` nella pagina Chiavi di Azure.The endpoint key of the resource on the Azure Keys page.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **ENDPOINT_URI e così via.** | Il valore dell'endpoint di `Speech` fatturazione è disponibile nella pagina Panoramica di Azure.The billing endpoint value is available on the Azure Overview page. | Vedere [la raccolta dei parametri obbligatori](speech-container-howto.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia. Per altre informazioni, vedere[Fatturazione](#billing-configuration-setting).
> Il valore ApiKey è la chiave della pagina Chiavi risorse vocali di Azure.The ApiKey value is the **Key** from the Azure Speech Resource keys page.

## <a name="speech-container-docker-examples"></a>Esempi di Docker del contenitore vocale

Gli esempi Docker seguenti riguardano il contenitore Speech.The following Docker examples are for the Speech container.

## <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Esempio di base per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Esempio di registrazione per la sintesi vocaleLogging example for Speech-to-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Esempio di base per la sintesi vocale personalizzata

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Esempio di registrazione per la sintesi vocale personalizzata

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Sintesi vocale](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Esempio di base per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Esempio di registrazione per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Esempio di base per la sintesi vocale personalizzataBasic example for Custom Text-to-speech

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Esempio di registrazione per la sintesi vocale personalizzataLogging example for Custom Text-to-speech

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Come installare ed eseguire i contenitori](speech-container-howto.md)
