---
title: Configurare i contenitori-Analisi del testo
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
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795299"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurare i contenitori docker di Analisi del testo

Analisi del testo fornisce a ogni contenitore un framework di configurazione comune che consente di configurare e gestire con facilità le impostazioni di archiviazione, registrazione, telemetria e sicurezza dei contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](how-tos/text-analytics-how-to-install-containers.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave di configurazione valida per la risorsa _Analisi del testo_ specificata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **analisi del testo** gestione delle risorse, in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa di _Analisi del testo_ in Azure usata per controllare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere un URI dell'endpoint valido per una risorsa di _Analisi del testo_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica **analisi del testo** , con etichetta `Endpoint`

|Obbligatorio| Name | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | String | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [raccolta dei parametri obbligatori](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

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

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](how-tos/text-analytics-how-to-install-containers.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| Name | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | String | I contenitori di Analisi del testo non la usano.|
|Facoltativo| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga**: i comandi di Docker nelle sezioni seguenti usano la barra rovesciata `\`come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine**degli argomenti: non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.

Sostituire {_nome_argomento_} con i propri valori:

| Placeholder | Valore | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave dell'endpoint della risorsa `Text Analytics` disponibile nella pagina chiavi di `Text Analytics` di Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Il valore dell'endpoint di fatturazione è disponibile nella pagina Panoramica di Azure `Text Analytics`.| Vedere [raccolta di parametri obbligatori](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](how-tos/text-analytics-how-to-install-containers.md#billing).
> Il valore ApiKey è la **chiave** della pagina delle chiavi delle risorse di Azure `Text Analytics`. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Analisi del sentiment](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](how-tos/text-analytics-how-to-install-containers.md)
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
