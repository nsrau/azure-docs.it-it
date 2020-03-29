---
title: Configurare i contenitori - Analisi del testo
titleSuffix: Azure Cognitive Services
description: Analisi del testo fornisce a ogni contenitore un framework di configurazione comune che consente di configurare e gestire con facilità le impostazioni di archiviazione, registrazione, telemetria e sicurezza dei contenitori.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795299"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurare i contenitori docker di Analisi del testo

Analisi del testo fornisce a ogni contenitore un framework di configurazione comune che consente di configurare e gestire con facilità le impostazioni di archiviazione, registrazione, telemetria e sicurezza dei contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)impostazioni [`Eula`](#eula-setting) , e vengono utilizzate insieme ed è necessario fornire valori validi per tutti e tre; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](how-tos/text-analytics-how-to-install-containers.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave di configurazione valida per la risorsa _Analisi del testo_ specificata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: gestione delle risorse **di Analisi del testo,** in **ChiaviAzure** portal: Text Analytics resource management, under Keys

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa di _Analisi del testo_ in Azure usata per controllare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa _ Text Analytics in Azure.You must specify a value for this configuration setting, and the value must be a valid endpoint URI for a __Text Analytics_ resource on Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica dell'analisi del **testo,** con etichettaAzure portal: Text Analytics Overview, labeled`Endpoint`

|Obbligatoria| Nome | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | string | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [Raccolta dei parametri obbligatori](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

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

Il contenitore Analisi del testo non usa montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| Nome | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | string | I contenitori di Analisi del testo non la usano.|
|Facoltativo| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di**riga: i comandi docker nelle sezioni seguenti utilizzano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | valore | Formato o esempio |
|-------------|-------|---|
| **"API_KEY"** | Chiave dell'endpoint `Text Analytics` della risorsa `Text Analytics` disponibile nella pagina Chiavi di Azure.The endpoint key of the resource available on the Azure Keys page. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **ENDPOINT_URI e così via.** | Il valore dell'endpoint di `Text Analytics` fatturazione è disponibile nella pagina Panoramica di Azure.The billing endpoint value is available on the Azure Overview page.| Vedere [la raccolta dei parametri obbligatori](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](how-tos/text-analytics-how-to-install-containers.md#billing).
> Il valore ApiKey è la `Text Analytics` chiave della pagina Chiavi risorsa di Azure.The ApiKey value is the **Key** from the Azure Resource keys page. 

#### <a name="key-phrase-extraction"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Analisi del sentimento](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](how-tos/text-analytics-how-to-install-containers.md)
* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services Containers](../cognitive-services-container-support.md)
