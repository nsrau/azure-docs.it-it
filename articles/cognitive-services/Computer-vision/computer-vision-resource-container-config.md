---
title: Configurare i contenitori - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Configurare varie impostazioni per i contenitori di riconoscimento del testo in Visione artificiale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 186f2f60aad15b336265114d7c85c757e0dd333f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102293"
---
# <a name="configure-recognize-text-docker-containers"></a>Configurare i contenitori del Docker di riconoscimento del testo

L'ambiente di runtime del contenitore **Riconoscimento del testo** si configura mediante gli argomenti del comando `docker run`. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](computer-vision-how-to-install-containers.md) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L' `ApiKey` impostazione specifica la chiave `Cognitive Services` di risorsa di Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per APIKEY e il valore deve essere una chiave valida per la risorsa _Servizi cognitivi_ specificata per l' [`Billing`](#billing-configuration-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** Gestione delle risorse, in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L' `Billing` impostazione specifica l'URI dell'endpoint della risorsa _Servizi cognitivi_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa di _Servizi cognitivi_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** Panoramica, con etichetta`Endpoint`

Ricordarsi di aggiungere `vision/v1.0` il routing all'URI dell'endpoint, come illustrato nella tabella seguente. 

|Obbligatoria| Name | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | String | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

I contenitori Visione artificiale non usano montaggi di input o di output per l'archiviazione di dati di servizio o di training. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](computer-vision-how-to-install-containers.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Non consentito| `Input` | String | I contenitori di Visione artificiale non la usano.|
|Facoltativo| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](computer-vision-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Ricordarsi di aggiungere `vision/v1.0` il routing all'URI dell'endpoint, come illustrato nella tabella seguente. 

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | Value | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave dell'endpoint della `Computer Vision` risorsa nella pagina chiavi di Azure. `Computer Vision` | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Il valore dell'endpoint di fatturazione è disponibile nella `Computer Vision` pagina Panoramica di Azure.| Vedere [raccolta di parametri obbligatori](computer-vision-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](computer-vision-how-to-install-containers.md#billing).
> Il valore APIKEY è la **chiave** della pagina chiavi `Cognitive Services` di risorsa di Azure. 

## <a name="recognize-text-container-docker-examples"></a>Esempi di contenitore di riconoscimento del testo Docker

Gli esempi Docker seguenti sono per il contenitore di riconoscimento del testo. 

### <a name="basic-example"></a>Esempio di base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Esempio di registrazione 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](computer-vision-how-to-install-containers.md)
