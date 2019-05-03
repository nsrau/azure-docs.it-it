---
title: Configurare i contenitori di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il contenitore di riconoscimento vocale
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 424396c7b92fb58993c74e672c4c3ea9c8f814c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026222"
---
# <a name="configure-speech-service-containers"></a>Configurare i contenitori dei servizi di riconoscimento vocale

I contenitori di riconoscimento vocale consentono ai clienti di compilare un'architettura delle applicazioni vocale che è ottimizzata per sfruttare i vantaggi di funzionalità del cloud affidabile e la località di edge. I contenitori di riconoscimento due vocale Supportiamo ora siano **per il riconoscimento vocale** e **sintesi vocale**. 

Il **Speech** ambiente di runtime contenitore viene configurato mediante il `docker run` argomenti del comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

# <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](speech-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per la chiave API e il valore deve essere una chiave valida per il _Speech_ risorsa specificata per il [ `Billing` ](#billing-configuration-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Del riconoscimento vocale** gestione delle risorse, in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

Il `Billing` impostazione specifica l'URI dell'endpoint del _vocale_ risorsa di Azure usato per controllare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI dell'endpoint valido per un _vocale_ risorse in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Del riconoscimento vocale** panoramica, con l'etichetta `Endpoint`

|Obbligatoria| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|Sì| `Billing` | String | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

I contenitori di riconoscimento vocale non usano input o output Monta per archiviare i dati del servizio o formazione. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](speech-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| NOME | Tipo di dati | DESCRIZIONE |
|-------|------|-----------|-------------|
|Non consentito| `Input` | string | I contenitori di riconoscimento vocale non usano questa proprietà.|
|Facoltativo| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](speech-container-howto.md#stop-the-container).

* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Value | Formato o esempio |
|-------------|-------|---|
|{BILLING_KEY} | La chiave di endpoint della risorsa di riconoscimento vocale. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Il valore dell'endpoint di fatturazione inclusa la regione.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing-configuration-setting).
> Il valore ApiKey è il **chiave** nella pagina chiavi del riconoscimento vocale di risorse di Azure. 

## <a name="speech-container-docker-examples"></a>Esempi di riconoscimento vocale contenitori Docker

Gli esempi di Docker seguenti sono per il contenitore di riconoscimento vocale. 

### <a name="basic-example-for-speech-to-text"></a>Esempio di base per il riconoscimento vocale in testo

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>Esempio di base per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>Esempio di registrazione per il riconoscimento vocale in testo

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Esempio di registrazione per la sintesi vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](speech-container-howto.md)