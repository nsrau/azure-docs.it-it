---
title: Configurare i contenitori
titlesuffix: Face - Azure Cognitive Services
description: Impostazioni di configurazione per i contenitori.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 218343db4d8ac7e35f58951ee177f5c288aed3d9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476418"
---
# <a name="configure-face-docker-containers"></a>Configurare i contenitori di Viso Docker

L'ambiente di runtime del contenitore **Viso** si configura mediante gli argomenti del comando `docker run`. Questo contenitore ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

Le impostazioni del contenitore sono [gerarchiche](#hierarchical-settings) e possono essere impostate tramite [variabili di ambiente](#environment-variable-settings) o [argomenti della riga di comando](#command-line-argument-settings) di Docker.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](face-how-to-install-containers.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave valida per la risorsa _Viso_ specificata per l'impostazione di configurazione [`Billing`](#billing-setting).

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Gestione risorse **Viso**, in **Chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Viso_ in Azure usata per misurare i dati di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI dell'endpoint valido per una risorsa _Viso_ in Azure.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: La panoramica **Viso**, con etichetta `Endpoint`

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Yes| `Billing` | string | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

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

I contenitori Viso non usano montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](face-how-to-install-containers.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Non consentito| `Input` | string | I contenitori Viso non la usano.|
|Facoltativo| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Impostazioni gerarchiche

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](face-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Valore | Formato o esempio |
|-------------|-------|---|
|{BILLING_KEY} | La chiave di endpoint della risorsa Viso. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Il valore dell'endpoint di fatturazione inclusa la regione.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](face-how-to-install-containers.md#billing).
> Il valore ApiKey è la **chiave** derivante dalla pagina delle chiavi della risorsa Viso di Azure. 

## <a name="face-container-docker-examples"></a>Esempi di contenitore Docker Viso

Gli esempi Docker seguenti sono per il contenitore Viso. 

### <a name="basic-example"></a>Esempio di base 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Esempio di registrazione con argomenti della riga di comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Esempio di registrazione con variabile di ambiente

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](face-how-to-install-containers.md)
