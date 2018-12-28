---
title: Configurare i contenitori
titlesuffix: Text Analytics - Azure Cognitive Services
description: Analisi del testo fornisce a ogni contenitore un framework di configurazione comune che consente di configurare e gestire con facilità le impostazioni di archiviazione, registrazione, telemetria e sicurezza dei contenitori.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 7e993b9ccc57359ac64186765b7b704535eb5a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086675"
---
# <a name="configure-containers"></a>Configurare i contenitori

Analisi del testo fornisce a ogni contenitore un framework di configurazione comune che consente di configurare e gestire con facilità le impostazioni di archiviazione, registrazione, telemetria e sicurezza dei contenitori.

## <a name="configuration-settings"></a>Impostazioni di configurazione

Le impostazioni di configurazione nei contenitori di Analisi del testo seguono un ordine gerarchico e tutti i contenitori usano una gerarchia condivisa, basata sulla struttura di primo livello seguente:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Autenticazione](#authentication-configuration-settings)
* [Fatturazione](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Registrazione](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

Per specificare le impostazioni di configurazione quando si crea un'istanza di un contenitore dai contenitori di Analisi del testo, è possibile usare le [variabili di ambiente](#configuration-settings-as-environment-variables) o gli [argomenti della riga di comando](#configuration-settings-as-command-line-arguments).

I valori delle variabili di ambiente sostituiscono i valori degli argomenti della riga di comando, che a loro volta sostituiscono i valori predefiniti per l'immagine del contenitore. In altre parole, se si specificano valori diversi in una variabile di ambiente e in un argomento della riga di comando per la stessa impostazione di configurazione, ad esempio `Logging:Disk:LogLevel`, e in seguito si crea un'istanza di un contenitore, per il contenitore di cui è stata creata l'istanza viene usato il valore nella variabile di ambiente.

### <a name="configuration-settings-as-environment-variables"></a>Impostazioni di configurazione come variabili di ambiente

Per specificare le impostazioni di configurazione, è possibile usare la [sintassi delle variabili di ambiente ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment).

Il contenitore legge le variabili di ambiente dell'utente quando viene creata un'istanza del contenitore. Se esiste una variabile di ambiente, il valore di tale variabile sostituisce il valore predefinito per l'impostazione di configurazione specificata. Il vantaggio dell'uso delle variabili di ambiente consiste nel fatto che più impostazioni di configurazione possono essere impostate prima di creare un'istanza dei contenitori e che più contenitori possono usare automaticamente lo stesso set di impostazioni di configurazione.

Ad esempio, i comandi seguenti usano una variabile di ambiente per impostare il livello di registrazione della console su [LogLevel.Information](https://msdn.microsoft.com) e quindi creano un'istanza di un contenitore dall'immagine del contenitore Analisi del sentiment. Il valore della variabile di ambiente sostituisce l'impostazione di configurazione predefinita.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Impostazioni di configurazione come argomenti della riga di comando

È possibile usare la [sintassi degli argomenti della riga di comando ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) per specificare le impostazioni di configurazione.

È possibile specificare le impostazioni di configurazione nel parametro `ARGS` facoltativo del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Il vantaggio di usare gli argomenti della riga di comando è che ogni contenitore può usare un set di impostazioni di configurazione diverso e personalizzato.

Ad esempio, il comando seguente crea un'istanza di un contenitore a partire dal contenitore Analisi del sentiment e imposta il livello di registrazione della console su LogLevel.Information, sostituendo l'impostazione di configurazione predefinita.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Impostazione di configurazione ApiKey

L'impostazione di configurazione `ApiKey` specifica la chiave di configurazione della risorsa di Analisi del testo in Azure usata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve corrispondere a una chiave di configurazione valida per la risorsa di Analisi del testo specificata per l'impostazione di configurazione [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](how-tos/text-analytics-how-to-install-containers.md#billing) (Fatturazione).

## <a name="applicationinsights-configuration-settings"></a>Impostazioni di configurazione di ApplicationInsights

Le impostazioni di configurazione della sezione `ApplicationInsights` consentono di aggiungere nel contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Application Insights offre funzionalità di monitoraggio avanzate del contenitore fino al livello di codice. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore senza attendere che vengano segnalati da un utente.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

| NOME | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `InstrumentationKey` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Impostazioni di configurazione di Authentication

Le impostazioni di configurazione di `Authentication` forniscono opzioni di sicurezza di Azure per il contenitore. Anche se le impostazioni di configurazione di questa sezione sono disponibili per tutti i contenitori di Analisi del testo, il modo in cui i valori di queste impostazioni vengono usati è specifico per ogni contenitore e possono essere presenti contenitori che non usano per niente questa sezione.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Authentication`.

| NOME | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `ApiKey` | string o array | Chiavi di sottoscrizione di Azure usate dal contenitore per accedere ad altre risorse di Azure, se necessarie per il contenitore.<br/> Se il contenitore usa più di una chiave di sottoscrizione, il relativo valore viene specificato come matrice di stringhe. In caso contrario, viene usato un valore stringa per specificare una sola chiave di sottoscrizione usata dal contenitore. |

## <a name="billing-configuration-setting"></a>Impostazione di configurazione Billing

L'impostazione di configurazione `Billing` specifica l'URI dell'endpoint della risorsa di Analisi del testo in Azure usata per controllare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere un URI dell'endpoint valido per una risorsa di Analisi del testo in Azure.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](how-tos/text-analytics-how-to-install-containers.md#billing) (Fatturazione).

## <a name="eula-configuration-setting"></a>Impostazione di configurazione Eula

L'impostazione di configurazione `Eula` indica che è stata accettata la licenza per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e tale valore deve essere impostato su `accept`.

> [!IMPORTANT]
> Le impostazioni di configurazione [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) vengono usate insieme ed è necessario fornire valori validi per tutte e tre, altrimenti il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](how-tos/text-analytics-how-to-install-containers.md#billing) (Fatturazione).

I contenitori di Servizi cognitivi sono concessi in licenza in base al [contratto](https://go.microsoft.com/fwlink/?linkid=2018657) che disciplina l'uso di Azure. Se non si dispone di tale contratto, si accetta che l'uso di Azure sia disciplinato dal [Contratto di Sottoscrizione Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), in cui sono incluse le [condizioni per l'uso dei Servizi Online](https://go.microsoft.com/fwlink/?linkid=2018760). Per le anteprime si accettano inoltre le [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Con l'uso del contenitore si acconsente a rispettare tali condizioni.

## <a name="fluentd-configuration-settings"></a>Impostazioni di configurazione di Fluentd

La sezione `Fluentd` gestisce le impostazioni di configurazione per [Fluentd](https://www.fluentd.org), un agente di raccolta dati open source per la registrazione unificata. I contenitori di Analisi del testo includono un provider di registrazione Fluentd che consente al contenitore di scrivere dati di log e, facoltativamente, dati delle metriche in un server Fluentd.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Fluentd`.

| NOME | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Host` | string | Indirizzo IP o nome host DNS del server Fluentd. |
| `Port` | Integer | Porta del server Fluentd.<br/> Il valore predefinito è 24224. |
| `HeartbeatMs` | Integer | Intervallo di heartbeat, espresso in millisecondi. Se prima della scadenza di questo intervallo è non stato inviato alcun traffico dell'evento, viene inviato un heartbeat al server Fluentd. Il valore predefinito è 60000 millisecondi (1 minuto). |
| `SendBufferSize` | Integer | Spazio di buffer di rete, espresso in byte, allocato per le operazioni di invio. Il valore predefinito è 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, espresso in millisecondi, per stabilire una connessione SSL/TLS con il server Fluentd. Il valore predefinito è 10000 millisecondi (10 secondi).<br/> Se `UseTLS` è impostato su false, questo valore viene ignorato. |
| `UseTLS` | boolean | Indica se il contenitore deve usare SSL/TLS per comunicare con il server Fluentd. Il valore predefinito è False. |

## <a name="logging-configuration-settings"></a>Impostazioni di configurazione di Logging

Le impostazioni di configurazione di `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni di configurazione e gli stessi valori per il contenitore e per un'applicazione ASP.NET Core. I contenitori di Analisi del testo supportano i provider di registrazione seguenti:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Provider di registrazione `Console` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.
* [Eseguire il debug](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Provider di registrazione `Debug` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.
* Disco  
  Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.  
  Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:  

  | NOME | Tipo di dati | Descrizione |
  |------|-----------|-------------|
  | `Format` | string | Formato di output dei file di log.<br/> **Nota:** per abilitare il provider di registrazione, questo valore deve essere impostato su `json`. Se questo valore viene specificato senza specificare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
  | `MaxFileSize` | Integer | Dimensione massima, espressa in megabyte (MB), di un file di log. Quando la dimensione del file di log corrente corrisponde a questo valore o lo supera, il provider di registrazione avvia un nuovo file di log. Se viene specificato -1, la dimensione del file di log è limitata solo dalla dimensione massima del file del montaggio di output eventualmente presente. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione del supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Impostazioni di configurazione dei montaggi

I contenitori Docker forniti dai contenitori di Analisi del testo sono progettati per essere senza stato e non modificabili. In altre parole, i file creati all'interno di un contenitore vengono archiviati in un livello di contenitore con accesso in scrittura, che è permanente solo se il contenitore è in esecuzione e non è facilmente accessibile. Se tale contenitore è stato arrestato o rimosso, i file creati all'interno vengono eliminati definitivamente.

Tuttavia, dal momento che si tratta di contenitori Docker, è possibile usare le opzioni di archiviazione di Docker, ad esempio volumi e montaggi di associazione, per leggere e scrivere i dati permanenti all'esterno del contenitore, se quest'ultimo supporta tale funzionalità. Per altre informazioni su come specificare e gestire le opzioni di archiviazione di Docker, vedere [Manage data in Docker](https://docs.docker.com/storage/) (Gestire dati in Docker).

> [!NOTE]
> Per queste impostazioni di configurazione non è in genere necessario modificare i valori, che vengono invece usati come destinazione quando si specificano i montaggi di input e output per il contenitore. Per altre informazioni su come specificare i montaggi di input e output, vedere [Montaggi di input e output](#input-and-output-mounts).

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Mounts`.

| NOME | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`. |
| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. |

### <a name="input-and-output-mounts"></a>Montaggi di input e output

Per impostazione predefinita, ogni contenitore può supportare un *montaggio di input* per eseguire operazioni di lettura e un *montaggio di output* per eseguire operazioni di lettura. Non è necessario tuttavia che i contenitori supportino i montaggi di input o output e ogni contenitore può usare entrambi per scopi specifici, oltre alle opzioni di registrazione supportate dai contenitori di Analisi del testo. Le tabella seguente indica i montaggi di input e output eventualmente supportati per ogni contenitore di Analisi del testo.

| Contenitore | Montaggio di input | Montaggio di output |
|-----------|-------------|--------------|
|[Estrazione frasi chiave](#working-with-key-phrase-extraction) | Non supportate | Facoltativo |
|[Rilevamento lingua](#working-with-language-detection) | Non supportate | Facoltativo |
|[Analisi del sentiment](#working-with-sentiment-analysis) | Non supportate | Facoltativo |

È possibile specificare il montaggio di input oppure output tramite l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usato per creare un'istanza di un contenitore da un'immagine di contenitore scaricata. Per impostazione predefinita, il montaggio di input usa `/input` come destinazione e il montaggio di output usa `/output` come destinazione. Qualsiasi opzione di archiviazione di Docker disponibile per l'host del contenitore Docker può essere specificata nell'opzione `--mount`.

Ad esempio, il comando seguente definisce un montaggio di associazione Docker nella cartella `D:\Output` nel computer host come montaggio di output e quindi crea un'istanza di un contenitore dall'immagine del contenitore di Analisi del sentiment, salvando i file di log in formato JSON per il montaggio di output.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
