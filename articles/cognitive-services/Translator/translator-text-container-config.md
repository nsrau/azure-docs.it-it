---
title: Configurare i contenitori-Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Traduzione testuale fornisce contenitori con un Framework di configurazione comune, in modo da poter configurare e gestire facilmente le impostazioni di archiviazione, registrazione e telemetria e le impostazioni di sicurezza per i contenitori.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507671"
---
# <a name="configure-translator-text-docker-containers"></a>Configurare Traduzione testuale contenitori Docker

Traduzione testuale fornisce ogni contenitore con un Framework di configurazione comune, in modo che sia possibile configurare e gestire con facilità le impostazioni di archiviazione, registrazione e telemetria e sicurezza per i contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Le impostazioni di configurazione del contenitore sono le seguenti:

|Obbligatorio|Impostazione|Scopo|
|--|--|--|
|No|[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No|[Fluentd](#fluentd-settings)|Scrive il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No|Proxy HTTP|Configura un proxy HTTP per le richieste in uscita.|
|No|[registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No|[Mounts](#mount-settings)|Legge e scrive i dati dal computer host al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> È necessario fornire l'impostazione [`Eula`](#eula-setting) con il valore di `accept`; in caso contrario, il contenitore non verrà avviato.

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

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

I contenitori di Traduzione testuale non utilizzano montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio dell'host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](how-to-install-containers.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| Name | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | String | I contenitori di Traduzione testuale non lo usano.|
|Facoltativo| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga**: i comandi di Docker nelle sezioni seguenti usano la barra rovesciata `\`come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine**degli argomenti: non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

## <a name="translator-text-container-docker-examples"></a>Esempi di Docker di Traduzione testuale container

Gli esempi di Docker seguenti sono per il contenitore Traduzione testuale.

### <a name="basic-example"></a>Esempio di base

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Esempio di registrazione

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](how-to-install-containers.md)
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
