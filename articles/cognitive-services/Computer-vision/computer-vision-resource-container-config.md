---
title: Configurare i contenitori - Visione artificiale
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come configurare le impostazioni obbligatorie e facoltative per i contenitori Di testo riconosciuto in Visione artificiale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718982"
---
# <a name="configure-computer-vision-docker-containers"></a>Configurare i contenitori Docker di Visione artificiale

Configurare l'ambiente di runtime del `docker run` contenitore Visione Artificiale utilizzando gli argomenti del comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)impostazioni [`Eula`](#eula-setting) , e vengono utilizzate insieme ed è necessario fornire valori validi per tutti e tre; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](computer-vision-how-to-install-containers.md) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica `Cognitive Services` la chiave di risorsa di Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere [`Billing`](#billing-configuration-setting) una chiave valida per la risorsa _Servizi cognitivi_ specificata per l'impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Gestione delle risorse **di Servizi cognitivi,** in **ChiaviAzure** portal: Cognitive Services Resource Management, under Keys

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Servizi cognitivi_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa _di Servizi cognitivi_ in Azure.You must specify a value for this configuration setting, and the value must be a valid endpoint URI for a Cognitive Services resource on Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica dei **servizi cognitivi,** con etichettaAzure portal: Cognitive Services Overview, labeled`Endpoint`

Ricordarsi di `vision/v1.0` aggiungere il routing all'URI dell'endpoint come illustrato nella tabella seguente. 

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
|Facoltativo| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](computer-vision-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di**riga: i comandi Docker nelle sezioni seguenti utilizzano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | valore | Formato o esempio |
|-------------|-------|---|
| **"API_KEY"** | Chiave dell'endpoint `Computer Vision` della risorsa `Computer Vision` nella pagina Chiavi di Azure.The endpoint key of the resource on the Azure Keys page. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **ENDPOINT_URI e così via.** | Il valore dell'endpoint di `Computer Vision` fatturazione è disponibile nella pagina Panoramica di Azure.The billing endpoint value is available on the Azure Overview page.| Vedere [la raccolta dei parametri obbligatori](computer-vision-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](computer-vision-how-to-install-containers.md#billing).
> Il valore ApiKey è la `Cognitive Services` chiave della pagina Chiavi risorsa di Azure.The ApiKey value is the **Key** from the Azure Resource keys page.

## <a name="container-docker-examples"></a>Esempi di Docker contenitore

Gli esempi Docker seguenti sono per il contenitore Read.The following Docker examples are for the Read container.

### <a name="basic-example"></a>Esempio di base

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Esempio di registrazione 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](computer-vision-how-to-install-containers.md).
