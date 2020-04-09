---
title: Come configurare un contenitore per l'API Rilevatore di anomalieHow to configure a container for Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: L'ambiente di runtime del contenitore dell'API Anomaly Detector viene configurato utilizzando gli argomenti del `docker run` comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875181"
---
# <a name="configure-anomaly-detector-containers"></a>Configurare i contenitori di Rilevamento anomalie

L'ambiente di runtime del contenitore **Anomaly Detector** viene configurato utilizzando gli argomenti del `docker run` comando. Questo contenitore ha diverse impostazioni obbligatorie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey (Chiave api)](#apikey-configuration-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No|[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Fatturazione](#billing-configuration-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No|[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No|[Proxy HTTP](#http-proxy-credentials-settings)|Configurare un proxy HTTP per le richieste in uscita.|
|No|[Registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No|[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)impostazioni [`Eula`](#eula-setting) , e vengono utilizzate insieme ed è necessario fornire valori validi per tutti e tre; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](anomaly-detector-container-howto.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere una chiave [`Billing`](#billing-configuration-setting) valida per la risorsa _Rilevatore anomalie_ specificata per l'impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Gestione delle risorse del rilevatore di anomalie, in **ChiaviAzure** portal: **Anomaly Detector's** Resource Management, under Keys

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Rilevatore anomalie_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI endpoint valido per una risorsa Rilevatore anomalie in Azure.You must specify a value for this configuration setting, and the value must be a valid endpoint URI for an _Anomaly Detector_ resource on Azure.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: panoramica **del rilevatore** di anomalie, etichettato`Endpoint`

|Obbligatoria| Nome | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | string | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [Raccolta dei parametri obbligatori](anomaly-detector-container-howto.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

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

I contenitori Rilevatore anomalie non usano supporti di input o output per archiviare i dati di training o del servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](anomaly-detector-container-howto.md#the-host-computer) potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio Docker e le autorizzazioni del percorso di montaggio dell'host. 

|Facoltativo| Nome | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | string | I contenitori del rilevatore di anomalie non utilizzano questo.|
|Facoltativo| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](anomaly-detector-container-howto.md#stop-the-container).

* **Carattere di continuazione di**riga : I comandi `\`Docker nelle sezioni seguenti utilizzano la barra rovesciata, , come carattere di continuazione di riga per una shell bash. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. Ad esempio, il carattere di continuazione di riga per windows è un accento circonflesso, `^`. Sostituire la barra rovesciata con l'accento circonflesso. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori Docker.

Sostituire il valore `{}`tra parentesi quadre, , con i propri valori:

| Segnaposto | valore | Formato o esempio |
|-------------|-------|---|
| **"API_KEY"** | Chiave dell'endpoint `Anomaly Detector` della risorsa `Anomaly Detector` nella pagina Chiavi di Azure.The endpoint key of the resource on the Azure Keys page. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **ENDPOINT_URI e così via.** | Il valore dell'endpoint di `Anomaly Detector` fatturazione è disponibile nella pagina Panoramica di Azure.The billing endpoint value is available on the Azure Overview page.| Vedere [la raccolta dei parametri obbligatori](anomaly-detector-container-howto.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](anomaly-detector-container-howto.md#billing).
> Il valore ApiKey è la chiave della pagina Chiavi risorsa rilevatore di anomalie di Azure.The ApiKey value is the **Key** from the Azure Anomaly Detector Resource keys page. 

## <a name="anomaly-detector-container-docker-examples"></a>Esempi di Docker del contenitore Rilevatore di anomalie

Gli esempi Docker seguenti sono per il contenitore Rilevatore di anomalie. 

### <a name="basic-example"></a>Esempio di base 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Esempio di registrazione con argomenti della riga di comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un contenitore Anomaly Detector nelle istanze del contenitore di AzureDeploy an Anomaly Detector container to Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Ulteriori informazioni sul servizio API Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
