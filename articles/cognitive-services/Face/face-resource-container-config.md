---
title: Configurare i contenitori-API viso
titleSuffix: Azure Cognitive Services
description: Impostazioni di configurazione per i contenitori.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: a07f088f7f5699a2698c0ea8fb0e853b3d287572
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101944"
---
# <a name="configure-face-docker-containers"></a>Configurare i contenitori di Viso Docker

L'ambiente di runtime del contenitore **Viso** si configura mediante gli argomenti del comando `docker run`. Questo contenitore ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Le impostazioni [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](face-how-to-install-containers.md#billing) (Fatturazione).

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per APIKEY e il valore deve essere una chiave valida per la risorsa _Servizi cognitivi_ specificata per l' [`Billing`](#billing-configuration-setting) impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** Gestione delle risorse, in **chiavi**

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L' `Billing` impostazione specifica l'URI dell'endpoint della risorsa _Servizi cognitivi_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa di _Servizi cognitivi_ in Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: **Servizi cognitivi** Panoramica, con etichetta`Endpoint`

Ricordarsi di aggiungere il routing del _volto_ all'URI dell'endpoint, come illustrato nell'esempio. 

|Obbligatoria| Name | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|Sì| `Billing` | String | URI dell'endpoint di fatturazione<br><br>Esempio:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Impostazioni di configurazione CloudAI

Le impostazioni di configurazione nella sezione `CloudAI` forniscono le opzioni specifiche per il contenitore proprie del rispettivo contenitore. Gli oggetti e le impostazioni seguenti sono supportati per il contenitore Viso nella sezione `CloudAI`

| NOME | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Storage` | Object | Lo scenario di archiviazione usato dal contenitore Viso. Per altre informazioni sugli scenari di archiviazione e sulle impostazioni associate per l'oggetto `Storage`, vedere [Impostazioni di uno scenario di archiviazione](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Impostazioni di uno scenario di archiviazione

Il contenitore Viso archivia BLOB, cache, metadati e dati della coda, a seconda di ciò che è stato archiviato. Ad esempio, gli indici di training e i risultati per un gruppo di persone di grandi dimensioni vengono archiviati come dati BLOB. Il contenitore Viso offre due scenari di archiviazione diversi durante l'interazione con e l'archiviazione di questi tipi di dati:

* Memoria  
  Tutti i quattro tipi di dati vengono archiviati in memoria. Non vengono distribuiti, né risultano persistenti. Se il contenitore Viso viene arrestato o rimosso, tutti i dati in archiviazione per tale contenitore vengono eliminati.  
  Questo è lo scenario di archiviazione predefinito per il contenitore Viso.
* Azure  
  Il contenitore Viso usa l'archiviazione di Azure e Azure Cosmos DB per distribuire questi quattro tipi di dati in un archivio permanente. I dati BLOB e della coda sono gestiti dall'archiviazione di Azure. I metadati e i dati della cache vengono gestiti da Azure Cosmos DB. Se il contenitore Viso viene arrestato o rimosso, tutti i dati in archiviazione per tale contenitore rimangono archiviati in Archiviazione di Azure e Azure Cosmos DB.  
  Le risorse usate per lo scenario di archiviazione di Azure hanno i seguenti requisiti aggiuntivi
  * La risorsa di archiviazione di Azure deve usare il tipo di account StorageV2
  * La risorsa di Azure Cosmos DB deve usare l'API Azure Cosmos DB per MongoDB

Gli scenari di archiviazione e le impostazioni di configurazione associate sono gestite dall'oggetto `Storage`, sotto la sezione di configurazione `CloudAI`. Le impostazioni di configurazione seguenti sono disponibili nell'oggetto `Storage`:

| Name | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `StorageScenario` | String | Lo scenario di archiviazione supportato dal contenitore. Sono disponibili i valori seguenti<br/>`Memory` - Valore predefinito. Il contenitore usa l'archiviazione non permanente, non distribuita e in memoria, per l'utilizzo temporaneo in un singolo nodo. Se il contenitore viene arrestato o rimosso, l'archiviazione per tale contenitore viene eliminata definitivamente.<br/>`Azure` - Il contenitore utilizza le risorse di Azure per l'archiviazione. Se il contenitore viene arrestato o rimosso, l'archiviazione per tale contenitore viene salvata in modo permanente.|
| `ConnectionStringOfAzureStorage` | String | La stringa di connessione per la risorsa di archiviazione di Azure usata dal contenitore.<br/>Questa impostazione si applica solo se `Azure` viene specificato per l'impostazione di configurazione `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | String | La stringa di connessione MongoDB per la risorsa di Azure Cosmos DB usata dal contenitore.<br/>Questa impostazione si applica solo se `Azure` viene specificato per l'impostazione di configurazione `StorageScenario`. |

Il comando seguente, ad esempio, specifica lo scenario di archiviazione di Azure e fornisce le stringhe di connessione di esempio per le risorse di archiviazione di Azure e Cosmos DB usate per archiviare i dati per il contenitore Viso.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Lo scenario di archiviazione viene gestito separatamente dai montaggi di input e output. È possibile specificare una combinazione di queste funzionalità per un singolo contenitore. Ad esempio, il comando seguente definisce un montaggio di associazione Docker nella cartella `D:\Output` nel computer host come montaggio di output, quindi crea un'istanza di un contenitore dall'immagine del contenitore Viso, salvando il file di log in formato JSON per il montaggio di output. Il comando specifica anche lo scenario di archiviazione di Azure e fornisce le stringhe di connessione di esempio per le risorse di archiviazione di Azure e Cosmos DB usate per archiviare i dati per il contenitore Viso.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

|Facoltativo| Name | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Non consentito| `Input` | String | I contenitori Viso non la usano.|
|Facoltativo| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio 

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](face-how-to-install-containers.md#stop-the-container).

* **Carattere di continuazione di riga**: I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
* **Ordine degli argomenti**: Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | Value | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave dell'endpoint della `Face` risorsa nella pagina chiavi di Azure. `Face` | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Il valore dell'endpoint di fatturazione è disponibile nella `Face` pagina Panoramica di Azure.| Vedere [raccolta di parametri obbligatori](face-how-to-install-containers.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](face-how-to-install-containers.md#billing).
> Il valore APIKEY è la **chiave** della pagina chiavi `Cognitive Services` di risorsa di Azure. 

## <a name="face-container-docker-examples"></a>Esempi di contenitore Docker Viso

Gli esempi Docker seguenti sono per il contenitore Viso. 

### <a name="basic-example"></a>Esempio di base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Esempio di registrazione 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](face-how-to-install-containers.md)
