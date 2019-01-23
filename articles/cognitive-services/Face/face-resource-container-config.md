---
title: Configurare i contenitori
titlesuffix: Face - Azure Cognitive Services
description: Impostazioni di configurazione per i contenitori.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: eabbe8d771520d5b1d5360a3d86b96d27ea08277
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359443"
---
# <a name="configure-containers"></a>Configurare i contenitori

Il contenitore Viso usa un framework di configurazione comune, in modo che si possano facilmente configurare e gestire le impostazioni di archiviazione, di registrazione e telemetria e di sicurezza per i contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Le impostazioni di configurazione nel contenitore Viso sono gerarchiche e tutti i contenitori usano una gerarchia condivisa, basata sulla seguente struttura di primo livello:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [autenticazione](#authentication-configuration-settings)
* [Fatturazione](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [registrazione](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

È possibile usare [variabili di ambiente](#configuration-settings-as-environment-variables) oppure gli [argomenti della riga di comando](#configuration-settings-as-command-line-arguments) per specificare le impostazioni di configurazione quando si crea un'istanza del contenitore Viso.

I valori di variabile di ambiente sostituiscono i valori degli argomenti della riga di comando, che a loro volta sostituiscono i valori predefiniti per l'immagine del contenitore. In altre parole, se si specificano valori diversi in una variabile di ambiente e in un argomento della riga di comando per la stessa impostazione di configurazione, ad esempio `Logging:Disk:LogLevel`, e in seguito si crea un'istanza di un contenitore, per il contenitore di cui è stata creata l'istanza viene usato il valore nella variabile di ambiente.

### <a name="configuration-settings-as-environment-variables"></a>Impostazioni di configurazione come variabili di ambiente

Per specificare le impostazioni di configurazione, è possibile usare la [sintassi delle variabili di ambiente ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment).

Il contenitore legge le variabili di ambiente dell'utente quando viene creata un'istanza del contenitore. Se esiste una variabile di ambiente, il valore di tale variabile sostituisce il valore predefinito per l'impostazione di configurazione specificata. Il vantaggio dell'uso delle variabili di ambiente consiste nel fatto che più impostazioni di configurazione possono essere impostate prima di creare un'istanza dei contenitori e che più contenitori possono utilizzare automaticamente lo stesso set di impostazioni di configurazione.

Ad esempio, i comandi seguenti usano una variabile di ambiente per configurare il livello di registrazione nella console in [LogLevel.Information](https://msdn.microsoft.com), quindi creano un'istanza di un contenitore dall'immagine del contenitore Viso. Il valore della variabile di ambiente sostituisce l'impostazione di configurazione predefinita.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Impostazioni di configurazione come argomenti della riga di comando

È possibile usare la [sintassi degli argomenti della riga di comando ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) per specificare le impostazioni di configurazione.

È possibile specificare le impostazioni di configurazione nel parametro `ARGS` facoltativo del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Il vantaggio di usare gli argomenti della riga di comando è che ogni contenitore può usare un set di impostazioni di configurazione diverso e personalizzato.

Ad esempio, i comandi seguenti permettono di creare un'istanza di un contenitore a partire dal contenitore Viso e di configurare il livello di registrazione nella console in LogLevel.Information, eseguendo l'override delle impostazioni di configurazione predefinite.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione apiKey

L'impostazione di configurazione `ApiKey` specifica la chiave di configurazione della risorsa Viso in Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere una chiave di configurazione valida per la risorsa Viso specificata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme, ed è necessario fornire i valori validi per tutte le tre impostazioni; in caso contrario, il contenitore non si avvia. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](face-how-to-install-containers.md#billing) (Fatturazione).

## <a name="applicationinsights-configuration-settings"></a>Impostazioni di configurazione di ApplicationInsights

Le impostazioni di configurazione della sezione `ApplicationInsights` consentono di aggiungere nel contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Application Insights offre funzionalità di monitoraggio avanzate del contenitore fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore senza attendere che vengano segnalati da un utente.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `InstrumentationKey` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Impostazioni di configurazione di Authentication

Le impostazioni di configurazione `Authentication` forniscono le opzioni di sicurezza di Azure per il contenitore. Sebbene siano disponibili le impostazioni di configurazione di questa sezione, il contenitore Viso non utilizza questa sezione.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Storage` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione di configurazione `Billing` specifica l'URI dell'endpoint della risorsa Viso in Azure usata per misurare i dati di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI dell'endpoint valido per una risorsa Viso in Azure.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme, ed è necessario fornire i valori validi per tutte le tre impostazioni; in caso contrario, il contenitore non si avvia. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Fatturazione](face-how-to-install-containers.md#billing).

## <a name="cloudai-configuration-settings"></a>Impostazioni di configurazione CloudAI

Le impostazioni di configurazione nella sezione `CloudAI` forniscono le opzioni specifiche per il contenitore proprie del rispettivo contenitore. Gli oggetti e le impostazioni seguenti sono supportati per il contenitore Viso nella sezione `CloudAI`

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Storage` | Oggetto | Lo scenario di archiviazione usato dal contenitore Viso. Per altre informazioni sugli scenari di archiviazione e sulle impostazioni associate per l'oggetto `Storage`, vedere [Impostazioni di uno scenario di archiviazione](#storage-scenario-settings) |

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

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `StorageScenario` | string | Lo scenario di archiviazione supportato dal contenitore. Sono disponibili i valori seguenti<br/>`Memory` - Valore predefinito. Il contenitore usa l'archiviazione non permanente, non distribuita e in memoria, per l'utilizzo temporaneo in un singolo nodo. Se il contenitore viene arrestato o rimosso, l'archiviazione per tale contenitore viene eliminata definitivamente.<br/>`Azure` - Il contenitore utilizza le risorse di Azure per l'archiviazione. Se il contenitore viene arrestato o rimosso, l'archiviazione per tale contenitore viene salvata in modo permanente.|
| `ConnectionStringOfAzureStorage` | string | La stringa di connessione per la risorsa di archiviazione di Azure usata dal contenitore.<br/>Questa impostazione si applica solo se `Azure` viene specificato per l'impostazione di configurazione `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | string | La stringa di connessione MongoDB per la risorsa di Azure Cosmos DB usata dal contenitore.<br/>Questa impostazione si applica solo se `Azure` viene specificato per l'impostazione di configurazione `StorageScenario`. |

Il comando seguente, ad esempio, specifica lo scenario di archiviazione di Azure e fornisce le stringhe di connessione di esempio per le risorse di archiviazione di Azure e Cosmos DB usate per archiviare i dati per il contenitore Viso.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Lo scenario di archiviazione viene gestito separatamente dai montaggi di input e output. È possibile specificare una combinazione di queste funzionalità per un singolo contenitore. Ad esempio, il comando seguente definisce un montaggio di associazione Docker nella cartella `D:\Output` nel computer host come montaggio di output, quindi crea un'istanza di un contenitore dall'immagine del contenitore Viso, salvando il file di log in formato JSON per il montaggio di output. Il comando specifica anche lo scenario di archiviazione di Azure e fornisce le stringhe di connessione di esempio per le risorse di archiviazione di Azure e Cosmos DB usate per archiviare i dati per il contenitore Viso.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>Impostazione di configurazione Eula

L'impostazione di configurazione `Eula` indica che è stata accettata la licenza per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere impostato su `accept`.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](face-how-to-install-containers.md#billing) (Fatturazione).

I contenitori di Servizi cognitivi sono concessi in licenza in base al [contratto](https://go.microsoft.com/fwlink/?linkid=2018657) che disciplina l'uso di Azure. Se non si dispone di tale contratto, si accetta che l'uso di Azure sia disciplinato dal [Contratto di Sottoscrizione Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), in cui sono incluse le [condizioni per l'utilizzo dei Servizi Online](https://go.microsoft.com/fwlink/?linkid=2018760). Per le anteprime si accettano inoltre le [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Con l'uso del contenitore si acconsente a rispettare tali condizioni.

## <a name="fluentd-configuration-settings"></a>Impostazioni di configurazione di Fluentd

La sezione `Fluentd` consente di gestire le impostazioni di configurazione per [Fluentd](https://www.fluentd.org), un agente di raccolta dati open source per la registrazione unificata. Il contenitore Viso include un provider di registrazione di Fluentd che consente al contenitore di scrivere log e, facoltativamente, dati delle metriche in un server di Fluentd.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Fluentd`.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Host` | string | Indirizzo IP o nome host DNS del server Fluentd. |
| `Port` | Integer | Porta del server Fluentd.<br/> Il valore predefinito è 24224. |
| `HeartbeatMs` | Integer | Intervallo di heartbeat, espresso in millisecondi. Se prima della scadenza di questo intervallo è non stato inviato alcun traffico dell'evento, viene inviato un heartbeat al server Fluentd. Il valore predefinito è 60000 millisecondi (1 minuto). |
| `SendBufferSize` | Integer | Spazio di buffer di rete, espresso in byte, allocato per le operazioni di invio. Il valore predefinito è 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, espresso in millisecondi, per stabilire una connessione SSL/TLS con il server Fluentd. Il valore predefinito è 10000 millisecondi (10 secondi).<br/> Se `UseTLS` è impostato su false, questo valore viene ignorato. |
| `UseTLS` | boolean | Indica se il contenitore deve usare SSL/TLS per comunicare con il server Fluentd. Il valore predefinito è False. |

## <a name="logging-configuration-settings"></a>Impostazioni di configurazione di Logging

Le impostazioni di configurazione di `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni di configurazione e gli stessi valori per il contenitore e per un'applicazione ASP.NET Core. I provider di registrazione seguenti sono supportati dal contenitore Viso:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Provider di registrazione `Console` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.
* [Eseguire il debug](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Provider di registrazione `Debug` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.
* Disco  
  Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.  
  Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:  

  | NOME | Tipo di dati | DESCRIZIONE |
  |------|-----------|-------------|
  | `Format` | string | Formato di output dei file di log.<br/> **Nota:** per abilitare il provider di registrazione, questo valore deve essere impostato su `json`. Se questo valore viene specificato senza specificare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
  | `MaxFileSize` | Integer | Dimensione massima, espressa in megabyte (MB), di un file di log. Quando la dimensione del file di log corrente corrisponde a questo valore o lo supera, il provider di registrazione avvia un nuovo file di log. Se viene specificato -1, la dimensione del file di log è limitata solo dalla dimensione massima del file del montaggio di output eventualmente presente. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione del supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Impostazioni di configurazione di montaggio

I contenitori Docker forniti da Viso sono progettati per essere senza stato e non modificabili. In altre parole, i file creati all'interno di un contenitore vengono archiviati in un livello di contenitore con accesso in scrittura, che è permanente solo se il contenitore è in esecuzione e non è facilmente accessibile. Se tale contenitore è stato arrestato o rimosso, i file creati all'interno vengono eliminati definitivamente.

Tuttavia, dal momento che si tratta di contenitori Docker, è possibile usare le opzioni di archiviazione di Docker, ad esempio volumi e montaggi di associazione, per leggere e scrivere i dati permanenti all'esterno del contenitore, se quest'ultimo supporta tale funzionalità. Per altre informazioni su come specificare e gestire le opzioni di archiviazione di Docker, vedere [Manage data in Docker](https://docs.docker.com/storage/) (Gestire dati in Docker).

> [!NOTE]
> Per queste impostazioni di configurazione non è in genere necessario modificare i valori, che vengono invece usati come destinazione quando si specificano i montaggi di input e output per il contenitore. Per altre informazioni su come specificare i montaggi di input e output, vedere [Montaggi di input e output](#input-and-output-mounts).

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Mounts`.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`. |
| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. |

### <a name="input-and-output-mounts"></a>Montaggi di input e output

Per impostazione predefinita, ogni contenitore può supportare un *montaggio di input*, da cui il contenitore può leggere i dati e un *montaggio di output*, in cui il contenitore può scrivere i dati. Tuttavia, i contenitori non devono supportare i montaggi di input o output, e ogni contenitore può usare entrambi per scopi specifici dei contenitori oltre alle opzioni di registrazione supportate dal contenitore Viso.

Il contenitore Viso non supporta i montaggi di input e output e supporta facoltativamente i montaggi di output.

È possibile specificare il montaggio di input o output tramite l'opzione `--mount` nel comando [di esecuzione docker ](https://docs.docker.com/engine/reference/commandline/run/) utilizzato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Per impostazione predefinita, il montaggio di input usa `/input` come destinazione e il montaggio di output usa `/output` come destinazione. Qualsiasi opzione di archiviazione di Docker disponibile per l'host del contenitore Docker può essere specificata nell'opzione `--mount`.

Ad esempio, il comando seguente definisce un montaggio di associazione Docker nella cartella `D:\Output` nel computer host come montaggio di output, quindi crea un'istanza di un contenitore dall'immagine del contenitore Viso, salvando il file di log in formato JSON per il montaggio di output.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

Il contenitore Viso non utilizza montaggi di input o output per archiviare i dati di training o di database. Al contrario, il contenitore Viso fornisce scenari di archiviazione per la gestione dei dati di training e di database. Per altre informazioni sull'uso di scenari di archiviazione, vedere [Impostazioni di uno scenario di archiviazione](#storage-scenario-settings).
