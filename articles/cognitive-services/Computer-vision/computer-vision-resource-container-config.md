---
title: Configurare i contenitori
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Impostazioni di configurazione per i contenitori in Visione artificiale.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 8df293878486a9cd4ded6e899871c30498c4b68f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634970"
---
# <a name="configure-containers"></a>Configurare i contenitori

In Visione artificiale è disponibile il contenitore di riconoscimento del testo con un framework di configurazione comune, in modo che sia possibile configurare e gestire archiviazione, registrazione e telemetria, nonché le impostazioni di sicurezza per i contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Le impostazioni di configurazione nel contenitore Visione artificiale sono gerarchiche e tutti i contenitori usano una gerarchia condivisa, basata sulla struttura di primo livello seguente:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Authentication](#authentication-configuration-settings)
* [Billing](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Logging](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

È possibile usare [variabili di ambiente](#configuration-settings-as-environment-variables) oppure [argomenti della riga di comando](#configuration-settings-as-command-line-arguments) per specificare le impostazioni di configurazione quando si crea un'istanza di un contenitore da contenitori di Visione artificiale.

I valori della variabile di ambiente sostituiscono quelli degli argomenti della riga di comando, che a loro volta sostituiscono i valori predefiniti per l'immagine del contenitore. In altre parole, se si specificano valori diversi in una variabile di ambiente e in un argomento della riga di comando per la stessa impostazione di configurazione, ad esempio `Logging:Disk:LogLevel`, e in seguito si crea un'istanza di un contenitore, viene usato il valore nella variabile di ambiente per il contenitore di cui è stata creata l'istanza.

### <a name="configuration-settings-as-environment-variables"></a>Impostazioni di configurazione come variabili di ambiente

È possibile usare la [sintassi delle variabili di ambiente ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) per specificare le impostazioni di configurazione.

Il contenitore legge le variabili di ambiente dell'utente quando viene creata un'istanza di contenitore. Se esiste una variabile di ambiente, il relativo valore viene sostituito al valore predefinito per l'impostazione di configurazione specificata. Il vantaggio dell'uso delle variabili di ambiente consiste nel fatto che più impostazioni di configurazione possono essere impostate prima di creare un'istanza dei contenitori e che più contenitori possono usare automaticamente lo stesso set di impostazioni di configurazione.

I comandi seguenti usano ad esempio una variabile di ambiente per configurare il livello di registrazione della console in [LogLevel.Information](https://msdn.microsoft.com) e quindi creano un'istanza di un contenitore dall'immagine del contenitore di riconoscimento del testo. Il valore della variabile di ambiente sostituisce l'impostazione di configurazione predefinita.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Impostazioni di configurazione come argomenti della riga di comando

È possibile usare la [sintassi degli argomenti della riga di comando ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) per specificare le impostazioni di configurazione.

È possibile specificare le impostazioni di configurazione nel parametro `ARGS` facoltativo del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Il vantaggio di usare gli argomenti della riga di comando consiste nel fatto che ogni contenitore può usare un set di impostazioni di configurazione diverso e personalizzato.

I comandi seguenti consentono ad esempio di creare un'istanza di un contenitore a partire dall'immagine di contenitore di riconoscimento del testo e di configurare il livello di registrazione della console in LogLevel.Information, sostituendo le impostazioni di configurazione predefinite.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione di configurazione `ApiKey` specifica la chiave di configurazione della risorsa Visione artificiale usata per tenere traccia delle informazioni di fatturazione per il contenitore. Per tale impostazione di configurazione, è necessario specificare un valore che deve essere una chiave di configurazione valida per la risorsa Visione artificiale indicata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario indicare valori validi per tutte le tre impostazioni. In caso contrario, il contenitore non si avvia. Per altre informazioni sull'uso di tali impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Fatturazione](computer-vision-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Impostazioni di configurazione ApplicationInsights

Le impostazioni di configurazione nella sezione `ApplicationInsights` consentono di aggiungere supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) nel contenitore. Application Insights offre funzionalità di monitoraggio avanzate del contenitore fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'uso del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore senza attendere che vengano segnalati da un utente.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

| Nome | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `InstrumentationKey` | String | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Impostazioni di configurazione di autenticazione

Le impostazioni di configurazione `Authentication` consentono di specificare le opzioni di sicurezza di Azure per il contenitore. Sebbene le impostazioni di configurazione di questa sezione siano disponibili, il contenitore di riconoscimento del testo non usa questa sezione.

## <a name="billing-configuration-setting"></a>Impostazione di configurazione di fatturazione

L'impostazione di configurazione `Billing` specifica l'URI dell'endpoint della risorsa Visione artificiale in Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. Per tale impostazione di configurazione, è necessario specificare un valore che deve essere un URI dell'endpoint valido per una risorsa Visione artificiale in Azure.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario indicare valori validi per tutte le tre impostazioni. In caso contrario, il contenitore non si avvia. Per altre informazioni sull'uso di tali impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Fatturazione](computer-vision-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Impostazione di configurazione Eula

L'impostazione di configurazione `Eula` indica che è stata accettata la licenza per il contenitore. Per tale impostazione di configurazione, è necessario specificare un valore che deve essere impostato su `accept`.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario indicare valori validi per tutte le tre impostazioni. In caso contrario, il contenitore non si avvia. Per altre informazioni sull'uso di tali impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Fatturazione](computer-vision-how-to-install-containers.md#billing).

## <a name="fluentd-configuration-settings"></a>Impostazioni di configurazione Fluentd

La sezione `Fluentd` consente di gestire le impostazioni di configurazione per [Fluentd](https://www.fluentd.org), un agente di raccolta dati open source per la registrazione unificata. Il contenitore Visione artificiale include un provider di registrazione Fluentd che consente al contenitore di scrivere log e, facoltativamente, dati delle metriche in un server di Fluentd.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Fluentd`.

| Nome | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Host` | String | Indirizzo IP o nome host DNS del server Fluentd. |
| `Port` | Integer | Porta del server Fluentd.<br/> Il valore predefinito è 24224. |
| `HeartbeatMs` | Integer | Intervallo di heartbeat in millisecondi. Se prima della scadenza di questo intervallo non è stato inviato alcun traffico dell'evento, viene inviato un heartbeat per il server Fluentd. Il valore predefinito è 60000 millisecondi (1 minuto). |
| `SendBufferSize` | Integer | Spazio di buffer di rete, in byte, allocato per operazioni di invio. Il valore predefinito è 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, espresso in millisecondi, per stabilire una connessione SSL/TLS con il server Fluentd. Il valore predefinito è 10000 millisecondi (10 secondi).<br/> Se `UseTLS` è impostato su false, questo valore viene ignorato. |
| `UseTLS` | Boolean | Indica se il contenitore deve usare SSL/TLS per comunicare con il server Fluentd. Il valore predefinito è False. |

## <a name="logging-configuration-settings"></a>Impostazioni di configurazione di registrazione

Le impostazioni di configurazione `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni e gli stessi valori di configurazione per il contenitore e per un'applicazione ASP.NET Core. I provider di registrazione seguenti sono supportati da contenitori di Visione artificiale:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Provider di registrazione `Console` di ASP.NET Core. Tutte le impostazioni di configurazione di ASP.NET Core e i valori predefiniti per questo provider di registrazione sono supportati.
* [Debug](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Provider di registrazione `Debug` di ASP.NET Core. Tutte le impostazioni di configurazione di ASP.NET Core e i valori predefiniti per questo provider di registrazione sono supportati.
* Disco  
  Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.  
  Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:  

  | Nome | Tipo di dati | Descrizione |
  |------|-----------|-------------|
  | `Format` | String | Formato di output per i file di log.<br/> **Nota:** questo valore deve essere impostato su `json` per abilitare il provider di registrazione. Se questo valore viene specificato senza indicare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
  | `MaxFileSize` | Integer | Dimensioni massime, in megabyte (MB), di un file di log. Quando le dimensioni del file di log corrente corrispondono o superano questo valore, un nuovo file di log viene avviato dal provider di registrazione. Se viene specificato -1, le dimensioni del file di log sono limitate solo dalle dimensioni massime del file, se presente, per il montaggio di output. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione di supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Impostazioni di configurazione di montaggio

I contenitori Docker offerti da Visione artificiale sono progettati per essere senza stato e non modificabili. In altre parole, i file creati all'interno di un contenitore vengono archiviati in un livello di contenitore con accesso in scrittura, permanente solo se il contenitore è in esecuzione e non è facilmente accessibile. Se tale contenitore è stato arrestato o rimosso, i file creati all'interno di tale contenitore vengono eliminati definitivamente.

Poiché si tratta di contenitori di Docker, è possibile tuttavia usare le opzioni di archiviazione Docker, ad esempio volumi e montaggi di associazione, per leggere e scrivere i dati permanenti all'esterno del contenitore, se quest'ultimo supporta l'opzione. Per altre informazioni su come specificare e gestire le opzioni di archiviazione di Docker, vedere [Manage data in Docker](https://docs.docker.com/storage/) (Gestire i dati in Docker).

> [!NOTE]
> Per queste impostazioni di configurazione in genere non è necessario modificare i valori, che vengono invece usati come destinazione quando si specificano i montaggi di input e output per il contenitore. Per altre informazioni su come specificare i montaggi di input e output, vedere [Montaggi di input e output](#input-and-output-mounts).

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Mounts`.

| Nome | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Input` | String | Destinazione del montaggio di input. Il valore predefinito è `/input`. |
| `Output` | String | Destinazione del montaggio di output. Il valore predefinito è `/output`. |

### <a name="input-and-output-mounts"></a>Montaggi di input e output

Per impostazione predefinita, ogni contenitore può supportare un *montaggio di input*, da cui il contenitore può leggere i dati, e un *montaggio di output*, in cui il contenitore può scrivere i dati. Non è necessario tuttavia che i contenitori supportino i montaggi di input o output e ogni contenitore può usare entrambi per scopi specifici, oltre alle opzioni di registrazione supportate dal contenitore Visione artificiale.

Il contenitore di riconoscimento del testo non supporta i montaggi di input e supporta facoltativamente i montaggi di output.

È possibile specificare il montaggio di input oppure output tramite l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Per impostazione predefinita, il montaggio di input usa `/input` come propria destinazione e il montaggio di output usa `/output` come destinazione. Ogni opzione di archiviazione di Docker disponibile per l'host del contenitore Docker può essere specificata nell'opzione `--mount`.

Il comando seguente, ad esempio, definisce un montaggio di associazione Docker nella cartella `D:\Output` nel computer host come montaggio di output e quindi crea un'istanza di un contenitore dall'immagine del contenitore di riconoscimento del testo, salvando il file di log in formato JSON per il montaggio di output.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
