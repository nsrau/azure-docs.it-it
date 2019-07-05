---
title: I canali di dati di telemetria in Azure Application Insights | Microsoft Docs
description: Come personalizzare i canali di dati di telemetria in Azure Application Insights SDK per .NET e .NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561354"
---
# <a name="telemetry-channels-in-application-insights"></a>Canali di dati di telemetria in Application Insights

I canali di dati di telemetria sono parte integrante del [Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md). Che gestiscono la memorizzazione nel buffer e la trasmissione di dati di telemetria al servizio Application Insights. Le versioni di .NET e .NET Core SDK hanno due canali di dati di telemetria predefiniti: `InMemoryChannel` e `ServerTelemetryChannel`. Questo articolo descrive ogni canale in modo dettagliato, incluse le procedure personalizzare il comportamento del canale.

## <a name="what-are-telemetry-channels"></a>Quali sono i canali di dati di telemetria?

I canali di dati di telemetria sono responsabili per la memorizzazione nel buffer gli elementi di telemetria e inviarli al servizio Application Insights, in cui vengono archiviati per l'esecuzione di query e l'analisi. Un canale di telemetria è una classe che implementa il [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) interfaccia.

Il `Send(ITelemetry item)` di un canale di telemetria viene chiamato dopo tutti gli inizializzatori di telemetria e i processori di telemetria vengono chiamati. Pertanto, qualsiasi elemento eliminato da un processore di telemetria non raggiunge il canale. `Send()` non viene in genere inviano elementi al back-end immediatamente. In genere, li memorizza nel buffer in memoria e li invia in batch, per la trasmissione efficiente.

[Live Stream metriche](live-stream.md) dispone anche di un canale personalizzato che consente il funzionamento di streaming in tempo reale dei dati di telemetria. Questo canale è indipendente dal canale di telemetria regolare e in questo documento non si applica a esso.

## <a name="built-in-telemetry-channels"></a>Canali di dati di telemetria predefiniti

Application Insights .NET e .NET Core SDK fornite con due canali incorporati:

* `InMemoryChannel`: Un canale leggero che memorizza nel buffer gli elementi in memoria fino a quando non vengono inviati. Gli elementi sono memorizzati nel buffer in memoria e scaricati una volta ogni 30 secondi oppure ogni volta che vengono memorizzati nel buffer di 500 elementi. Questo canale offre garanzie di affidabilità minimo perché non ritenta l'invio dati di telemetria dopo un errore. Questo canale inoltre non mantenere gli elementi su disco, in modo che eventuali elementi non inviati andranno persi definitivamente alla chiusura dell'applicazione (normale o No). Questo canale implementa una `Flush()` metodo che può essere utilizzato per forza scaricamento tutti gli elementi in memoria i dati di telemetria in modo sincrono. Questo canale è particolarmente adatto per le applicazioni a esecuzione breve in cui uno scaricamento sincrono è ideale.

    Questo canale è parte del pacchetto NuGet Microsoft. applicationinsights più grande e il canale predefinito che il SDK Usa quando nessun altro elemento è configurato.

* `ServerTelemetryChannel`: Un canale più avanzato con i criteri di ripetizione dei tentativi e la possibilità di archiviare i dati in un disco locale. Questo canale ritenta l'invio dati di telemetria, se si verificano errori temporanei. Questo canale Usa anche l'archiviazione su disco locale per mantenere gli elementi su disco durante le interruzioni di rete o volumi di dati di telemetria elevata. A causa di questi meccanismi di ripetizione dei tentativi e l'archiviazione su disco locale, questo canale viene considerato più affidabile ed è consigliato per tutti gli scenari di produzione. Questo canale è l'impostazione predefinita per [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) le applicazioni configurate in base alla documentazione ufficiale. Questo canale è ottimizzato per scenari di server con processi a esecuzione prolungata. Il [ `Flush()` ](#which-channel-should-i-use) metodo implementato da questo canale non è sincrona.

    Questo canale viene fornito come pacchetto Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e viene acquisito automaticamente quando si utilizza applicationinsights o Microsoft.ApplicationInsights.AspNetCore NuGet pacchetto.

## <a name="configure-a-telemetry-channel"></a>Configurare un canale di telemetria

Per configurare un canale di telemetria, impostarlo per la configurazione della telemetria attivi. Per le applicazioni ASP.NET, configurazione comporta l'impostazione l'istanza del canale dati di telemetria `TelemetryConfiguration.Active`, o modificando `ApplicationInsights.config`. Per le applicazioni ASP.NET Core, configurazione implica l'aggiunta del canale per il contenitore di inserimento delle dipendenze.

Le sezioni seguenti mostrano esempi di configurazione di `StorageFolder` impostazione per il canale in diversi tipi di applicazioni. `StorageFolder` è solo una delle impostazioni configurabili. Per l'elenco completo delle impostazioni di configurazione, vedere [la sezione Impostazioni](telemetry-channels.md#configurable-settings-in-channels) più avanti in questo articolo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configurazione mediante applicationinsights. config per applicazioni ASP.NET

La sezione seguente dal [applicationinsights. config](configuration-with-applicationinsights-config.md) Mostra le `ServerTelemetryChannel` canale configurato con `StorageFolder` impostata su un percorso personalizzato:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configurazione nel codice per le applicazioni ASP.NET

Il codice seguente consente di impostare un'istanza di 'ServerTelemetryChannel' con `StorageFolder` impostata su un percorso personalizzato. Aggiungere questo codice all'inizio dell'applicazione, in genere `Application_Start()` metodo nel file Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configurazione nel codice per applicazioni ASP.NET Core

Modificare il `ConfigureServices` metodo di `Startup.cs` classe come illustrato di seguito:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> La configurazione del canale usando `TelemetryConfiguration.Active` non è consigliato per le applicazioni ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configurazione nel codice per le applicazioni console.NET/.NET Core

Per le app di console, il codice è lo stesso per .NET Core e .NET:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Dettagli operativi di ServerTelemetryChannel

`ServerTelemetryChannel` archivi che arrivano gli elementi in un buffer in memoria. Gli elementi vengono serializzati, compressi e archiviati in un `Transmission` istanza una volta ogni 30 secondi, o quando sono stati memorizzati 500 elementi. Un singolo `Transmission` istanza contiene elementi fino a 500 e rappresenta un batch di dati di telemetria che ha inviato tramite una singola chiamata HTTPS al servizio Application Insights.

Per impostazione predefinita, un massimo di 10 `Transmission` istanze possono essere inviate in parallelo. Se i dati di telemetria è in arrivo in base alle tariffe più veloci, o se la rete o Application Insights esegue il backup finale è lenta, `Transmission` le istanze vengono archiviate in memoria. La capacità predefinita di questa in memoria `Transmission` buffer è 5 MB. Quando viene superata la capacità in memoria, `Transmission` le istanze vengono archiviate nel disco locale con un limite massimo di 50 MB. `Transmission` le istanze vengono archiviate nel disco locale anche quando sono presenti problemi di rete. Solo gli elementi che vengono archiviati in un disco locale vengono conservati dopo un arresto anomalo dell'applicazione. Vengono inviati ogni volta che l'applicazione viene avviata nuovamente.

## <a name="configurable-settings-in-channels"></a>Impostazioni configurabili nei canali

Per l'elenco completo di impostazioni configurabili per ogni canale, vedere:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Configurare le impostazioni usate più comunemente per `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: La quantità massima di memoria, espressa in byte, utilizzata dal canale alle trasmissioni del buffer in memoria. Quando viene raggiunta questa capacità, i nuovi elementi vengono archiviati direttamente nel disco locale. Il valore predefinito è 5 MB. Impostazione di un valore superiore determina minore utilizzo disco, ma ricordare che elementi nella cache andranno persi se l'arresto anomalo dell'applicazione.

1. `MaxTransmissionSenderCapacity`: Il numero massimo di `Transmission` istanze che verranno inviate ad Application Insights nello stesso momento. Il valore predefinito è 10. Questa impostazione può essere configurata su un numero più alto, che è consigliato quando viene generata un'enorme quantità di dati di telemetria. Volume elevato in genere si verifica durante il test di carico o quando campionamento viene disattivato.

1. `StorageFolder`: La cartella che viene utilizzata dal canale per memorizzare gli elementi su disco in base alle esigenze. In Windows, % LOCALAPPDATA % o % TEMP % viene usato se viene specificato in modo esplicito alcun altro percorso. In ambienti diversi da Windows, è necessario specificare che un percorso valido o dati di telemetria non vengono archiviati nel disco locale.

## <a name="which-channel-should-i-use"></a>Quale canale è consigliabile usare?

`ServerTelemetryChannel` è consigliato per la maggior parte degli scenari di produzione che coinvolgono applicazioni a esecuzione prolungata. Il `Flush()` metodo implementato dal `ServerTelemetryChannel` non è sincrono, e inoltre non è garantito l'invio di tutti gli elementi in sospeso da memoria o disco. Se si usa questo canale negli scenari in cui l'applicazione sta per arrestare, è consigliabile che si introduce un ritardo dopo la chiamata `Flush()`. La quantità esatta di ritardo che potrebbe richiedere non è prevedibile. Dipende da fattori quali il numero di elementi o `Transmission` istanze sono in memoria, quanti sono su disco, quanti sono trasmessi al back-end e indica se il canale si trova all'interno di scenari di Backoff esponenziali.

Se è necessario eseguire uno scaricamento sincrono, è consigliabile usare `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Il canale di Application Insights garantisce il recapito dei dati di telemetria? In caso contrario, quali sono gli scenari in cui i dati di telemetria può essere perso?

La risposta breve è che nessuno dei canali incorporati garantiscono una tipo di transazione del recapito di dati di telemetria al back-end. `ServerTelemetryChannel` è più avanzata rispetto a `InMemoryChannel` per recapito affidabile, ma anche effettua solo un tentativo migliore sforzo per inviare dati di telemetria. I dati di telemetria possono comunque andranno persi in varie situazioni, incluso questi scenari comuni:

1. Elementi nella cache andranno persi quando l'arresto anomalo dell'applicazione.

1. I dati di telemetria viene persa durante periodi prolungati di problemi di rete. I dati di telemetria vengono archiviati nel disco locale durante le interruzioni di rete o quando si verificano problemi con il back-end di Application Insights. Tuttavia, vengono rimossi elementi più vecchi di 24 ore.

1. I percorsi di disco predefinito per l'archiviazione dei dati di telemetria in Windows sono % LOCALAPPDATA % o % TEMP %. Questi percorsi vengono in genere locali nel computer. Se l'applicazione viene eseguita la migrazione fisica da una posizione a un'altra, i dati di telemetria archiviati nel percorso originale viene perso.

1. Nelle App Web in Windows, il percorso di archiviazione su disco predefinito è D:\local\LocalAppData. Questo percorso non è persistente. Vengono cancellato in riavvii dell'app, scalabilità-perdite di dati e altri tali operazioni, causando una perdita di dati di telemetria archiviati vengono eliminati. È possibile sostituire il valore predefinito e specificare l'archiviazione in un percorso persistente come d:\home. Tuttavia, tali posizioni persistenti vengono gestite da Archiviazione remota e pertanto possono essere lenti.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel funziona in sistemi diversi da Windows?

Anche se il nome del relativo pacchetto e lo spazio dei nomi include "WindowsServer", questo canale è supportato su sistemi diversi da Windows, con l'eccezione seguente. In sistemi diversi da Windows, il canale non crea una cartella di archiviazione locale per impostazione predefinita. È necessario creare una cartella di archiviazione locale e configurare il canale per usarlo. Dopo l'archiviazione locale è stata configurata, il canale funziona allo stesso modo in tutti i sistemi.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>L'SDK crea una risorsa di archiviazione locale temporanea? I dati vengono crittografati nell'archiviazione?

il SDK archivia gli elementi di telemetria nell'archiviazione locale durante i problemi di rete o la limitazione delle richieste. Questi dati non vengono crittografati in locale.

Per i sistemi Windows, il SDK automaticamente creata una cartella locale temporanea nella directory % LOCALAPPDATA % o % TEMP % e limita l'accesso per gli amministratori e solo l'utente corrente.

Per i sistemi diversi da Windows, nessuna risorsa di archiviazione locale viene creato automaticamente dal SDK e pertanto non vengono archiviati dati localmente per impostazione predefinita. È possibile creare manualmente una directory di archiviazione e configurare il canale per usarlo. In questo caso, si è responsabile di garantire che la directory sia protetto.
Altre informazioni, vedere [privacy e protezione dei dati](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK open source
Come ogni SDK per Application Insights, i canali sono open source. Leggere e contribuire al codice o segnalare i problemi, in [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi di SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
