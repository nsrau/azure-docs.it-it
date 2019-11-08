---
title: Come configurare un contenitore per il riconoscimento del modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare il contenitore Riconoscimento modulo per analizzare i dati di moduli e tabelle.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a0b0d0d95e1ffd50faba19f1665ea5dae737b124
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796141"
---
# <a name="configure-form-recognizer-containers"></a>Configurare i contenitori di Riconoscimento modulo

I contenitori di Riconoscimento modulo di Azure consentono di creare un'architettura per le applicazioni ottimizzata per sfruttare i vantaggi delle funzionalità cloud avanzate e della posizione fisica dei dispositivi perimetrali.

Per configurare l'ambiente di runtime dei contenitori di Riconoscimento modulo, si usano gli argomenti del comando `docker run`. Questo contenitore prevede diverse impostazioni obbligatorie e alcune facoltative. Per alcuni esempi, vedere la sezione ["Comandi docker run di esempio"](#example-docker-run-commands). Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme. È necessario specificare valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](form-recognizer-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. Il valore di ApiKey deve essere una chiave valida per la risorsa _Riconoscimento modulo_ specificata per `Billing` nella sezione "Impostazione di configurazione Billing".

Questa impostazione si trova nel portale di Azure, nella sezione **Chiavi** di **Form Recognizer Resource Management** (Gestione risorse di Riconoscimento modulo).

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa di _Riconoscimento modulo_ in Azure usata per misurare i dati di fatturazione per il contenitore. Il valore per questa impostazione deve essere un URI dell'endpoint valido per una risorsa di _Riconoscimento modulo_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione si trova nel portale di Azure, nella sezione **Endpoint** di **Form Recognizer Overview** (Panoramica di Riconoscimento modulo).

|Obbligatorio| Name | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | String | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [raccolta dei parametri obbligatori](form-recognizer-container-howto.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [`docker run` ](https://docs.docker.com/engine/reference/commandline/run/).

Per il contenitore di Riconoscimento modulo è necessario un montaggio di input e un montaggio di output. Il montaggio di input può essere di sola lettura ed è necessario per accedere ai dati usati per il training e l'assegnazione del punteggio. Il montaggio di output deve essere accessibile in scrittura e viene usato per archiviare i modelli e i dati temporanei.

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](form-recognizer-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni dell'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host.

|Facoltativo| Name | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Obbligatorio| `Input` | String | Destinazione del montaggio di input. Il valore predefinito è `/input`.    <br><br>Esempio:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obbligatorio| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`.  <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`. Una volta avviata, l'esecuzione del contenitore continua finché non la si [arresta](form-recognizer-container-howto.md#stop-the-container).

* **Carattere di continuazione di riga**: i comandi di Docker nelle sezioni seguenti usano una barra rovesciata (\\) come carattere di continuazione di riga. Sostituire o rimuovere questo carattere in base ai requisiti del sistema operativo host.
* **Ordine**degli argomenti: non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

Sostituire {_argument_name_} nella tabella seguente con i propri valori:

| Placeholder | Valore |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | La chiave usata per avviare il contenitore. È disponibile nella pagina Form Recognizer Keys (Chiavi di Riconoscimento modulo) del portale di Azure. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina Form Recognizer Overview (Panoramica di Riconoscimento modulo) del portale di Azure.|
| **{COMPUTER_VISION_API_KEY}** | La chiave è disponibile nella pagina Computer Vision API Keys (Chiavi dell'API Visione artificiale) del portale di Azure.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | L'endpoint di fatturazione. Se si usa una risorsa di Visione artificiale basata sul cloud, il valore dell'URI è disponibile nella pagina Computer Vision API Overview (Panoramica dell'API Visione artificiale) del portale di Azure. Se si usa un contenitore *cognitive-Services-Recognize-text* , usare l'URL dell'endpoint di fatturazione passato al contenitore nel comando `docker run`. |

Per informazioni dettagliate su come ottenere questi valori, vedere [raccolta dei parametri obbligatori](form-recognizer-container-howto.md#gathering-required-parameters) .

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Per eseguire il contenitore, specificare le opzioni `Eula`, `Billing` e `ApiKey`. In caso contrario, il contenitore non si avvia. Per altre informazioni, vedere[Fatturazione](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Esempi relativi ai contenitori di Riconoscimento modulo in Docker

Gli esempi di Docker seguenti si riferiscono al contenitore Riconoscimento modulo.

### <a name="basic-example-for-form-recognizer"></a>Esempio di base per Riconoscimento modulo

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Esempio di registrazione per Riconoscimento modulo

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Installare ed eseguire i contenitori](form-recognizer-container-howto.md).
