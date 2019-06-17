---
title: I canali di dati di telemetria in Azure Application Insights | Microsoft Docs
description: Come personalizzare i canali di dati di telemetria in Azure Application Insights SDK per.NET/.NET Core.
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
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255814"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel in Application Insights

TelemetryChannel è parte integrante del [Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md). Gestisce la memorizzazione nel buffer e la trasmissione di dati di telemetria al servizio Application Insights. Le versioni di .NET e .NET Core SDK hanno due TelemetryChannels predefiniti - `InMemoryChannel` e `ServerTelemetryChannel`. Questo articolo descrive ogni canale in modo dettagliato, incluso come gli utenti possono personalizzare il comportamento del canale.

## <a name="what-is-a-telemetrychannel"></a>Che cos'è un TelemetryChannel?

`TelemetryChannel` è responsabile della memorizzazione nel buffer e l'invio di elementi di telemetria al servizio Application Insights, dove viene archiviato per l'esecuzione di query e l'analisi. Si tratta di qualsiasi classe che implementa l'interfaccia [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

Il `Send(ITelemetry item)` di TelemetryChannel viene chiamato dopo che tutti `TelemetryInitializer`s e `TelemetryProcessor`s vengono chiamati. Ciò significa che tutti gli elementi eliminati `TelemetryProcessor` non raggiungeranno il canale. `Send()` non in genere invia gli elementi immediata nel back-end. Sono in genere memorizzata nel buffer in memoria e inviati in batch, per la trasmissione efficiente.

[LiveMetrics](live-stream.md) dispone anche di un canale personalizzato che viene usato lo streaming in tempo reale dei dati di telemetria. Questo canale è indipendente dal canale di telemetria regolare e in questo documento non si applica al canale utilizzato dal `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>TelemetryChannels predefiniti

.NET/.NET Core SDK di Application Insights viene fornito con due canali incorporati:

* **InMemoryChannel** 
 `InMemoryChannel` è un canale leggero, che memorizza nel buffer gli elementi in memoria fino a quando non viene inviato. Gli elementi sono memorizzati nel buffer in memoria e scaricati una volta ogni 30 secondi oppure ogni volta che sono memorizzati nel buffer di 500 elementi. Questo canale offre garanzie di affidabilità minimo perché non ritenta l'invio dati di telemetria al momento gli errori. Questo canale non mantiene gli elementi su disco, in modo che eventuali elementi non inviati vengono persi definitivamente alla chiusura dell'applicazione (normalmente oppure No). È presente un `Flush()` metodo implementato da questo canale, che può essere utilizzato per forza scaricamento tutti gli elementi in memoria i dati di telemetria in modo sincrono. Ciò è particolarmente adatto per le applicazioni a esecuzione breve in cui uno scaricamento sincrono è ideale.

    Questo canale viene fornito come parte di `Microsoft.ApplicationInsights` stesso del pacchetto, nuget ed è il canale predefinito il SDK Usa quando nessun altro elemento è configurato.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` è un canale più avanzato, che include i criteri di ripetizione dei tentativi e la possibilità di archiviare i dati sul disco locale. Questo canale ritenta l'invio dati di telemetria, se si verificano errori temporanei. Questo canale Usa anche l'archiviazione su disco locale per mantenere gli elementi su disco durante le interruzioni di rete o volumi di dati di telemetria elevata. A causa di questi meccanismi di ripetizione dei tentativi e l'archiviazione su disco locale, questo canale viene considerato più affidabile ed è consigliato per tutti gli scenari di produzione. Questo canale è l'impostazione predefinita per [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) applicazioni, che sono configurate in base ai documenti ufficiali collegati. Questo canale è ottimizzato per scenari di server dei processi a esecuzione prolungata. Il [ `Flush()` ](#which-channel-should-i-use) metodo implementato da questo canale non è sincrono.

    Questo canale viene fornito come pacchetto NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`e verrà portato automaticamente quando si usa uno dei pacchetti NuGet `Microsoft.ApplicationInsights.Web` o `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Configurazione TelemetryChannel

Canale di telemetria può essere configurato impostando il valore desiderato `TelemetryChannel` su attivo `TelemetryConfiguration`. Per le applicazioni Asp.Net, configurazione comporta l'impostazione `TelemetryChannel` sul `TelemetryConfiguration.Active`, o la modifica `ApplicationInsights.config`. Per le applicazioni ASP.NET Core, configurazione implica l'aggiunta del canale desiderato per il contenitore di inserimento delle dipendenze.

Seguito è riportato un esempio in cui la configurazione utente di `StorageFolder` per il canale. `StorageFolder` è solo una delle impostazioni configurabili. L'elenco completo delle impostazioni di configurazione è descritta [nella sezione Impostazioni](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Configurazione con applicationinsights. config per applicazioni ASP.NET

La sezione seguente dal [applicationinsights. config](configuration-with-applicationinsights-config.md) Mostra ServerTelemetryChannel configurato con `StorageFolder` impostata su un percorso personalizzato.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Configurazione nel codice per le applicazioni ASP.NET

Il codice seguente imposta ServerTelemetryChannel con `StorageFolder` impostata su un percorso personalizzato. Questo codice deve essere aggiunto all'inizio dell'applicazione, in genere nel metodo Application_Start) `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Configurazione nel codice per le applicazioni ASP.NET Core

Modificare `ConfigureServices` metodo `Startup.cs` classe come illustrato di seguito.

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

> [!NOTE]
> È importante notare che il canale utilizzando la configurazione `TelemetryConfiguration.Active` non è consigliato per le applicazioni ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Configurazione TelemetryChannel nel codice per le applicazioni di Console Core.NET/.NET

Per le app di Console, il codice è lo stesso per .NET e .NET Core e viene illustrato di seguito.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Dettagli operativi di ServerTelemetryChannel

Il `ServerTelemetryChannel` memorizza nel buffer gli elementi in arrivo in un buffer in memoria. È serializzato, compressi e archiviato in `Transmission` istanza una volta ogni 30 secondi o quando 500 elementi vengono memorizzati nel buffer. Un singolo `Transmission` istanza contiene elementi fino a 500 e rappresenta un batch di dati di telemetria inviato tramite una chiamata https singola al servizio Application Insights. Per impostazione predefinita, può esserci un massimo di 10 `Transmission`s inviati in parallelo. Se i dati di telemetria è in arrivo in base alle tariffe più veloce o back-end rete/Application Insights è lento, quindi `Transmission`ottenere archiviati in memoria. La capacità predefinita di questo buffer di trasmissione in memoria è 5 MB. Una volta supera la capacità in memoria, `Transmission`vengono archiviati sul disco locale per fino a 50 MB. `Transmission`vengono archiviati sul disco locale quando sono presenti anche alcuni problemi di rete. Solo gli elementi archiviati nel disco locale vengono conservati dopo un arresto anomalo dell'applicazione, che vengono inviati ogni volta che l'applicazione viene avviata nuovamente.

## <a name="configurable-settings-in-channel"></a>Impostazioni configurabili nel canale

L'elenco completo di impostazioni configurabili per ogni canale si trovano qui:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Le impostazioni per più comunemente usate `ServerTelemetryChannel` sono elencati di seguito:

1. `MaxTransmissionBufferCapacity` -Quantità di memoria, espressa in byte, utilizzata dal canale alle trasmissioni del buffer in memoria. Una volta raggiunto tale capacità, nuovi elementi verranno archiviati direttamente nel disco locale. Il valore predefinito è 5 MB. Impostando un valore più alto lead per l'utilizzo del disco più bassa, ma è importante notare che elementi nella cache andranno persi in caso di interruzioni dell'applicazione.

2. `MaxTransmissionSenderCapacity` -Quantità massima di `Transmission`che verrà inviati ad Application Insights nello stesso momento. Il valore predefinito è 10, ma può essere configurata su un numero più alto. Questo è consigliato quando viene generata un'enorme quantità di dati di telemetria, in genere quando si esegue il test di carico e/o quando campionamento viene disattivato.

3. `StorageFolder` -La cartella utilizzata dal canale per memorizzare gli elementi su disco in base alle esigenze. In Windows, % LocalAppData % o % Temp % viene utilizzato se non è configurato in modo esplicito. Negli ambienti di Non-Windows utente **necessario** configurare un percorso valido, senza i quali i dati di telemetria non vengono archiviati nel disco locale.

## <a name="which-channel-should-i-use"></a>Quale canale è consigliabile usare?

`ServerTelemetryChannel` è consigliato per la maggior parte degli scenari di produzione di applicazioni in esecuzione prolungata. Il `Flush()` implementazione del metodo `ServerTelemetryChannel` non è sincrono, e `Flush()` non garantisce l'invio di tutti gli elementi in sospeso da memoria o del disco. Se questo canale viene usato negli scenari in cui l'applicazione sta per arrestare, quindi è consigliabile eseguire un ritardo dopo la chiamata `Flush()` su questo canale. La quantità esatta di ritardo richiesto non è prevedibile, perché dipende da fattori quali il numero di elementi o `Transmissions` sono in memoria, quanti sono nel disco, quanti sono trasmessi al backup, e se il canale è in corso scenari di Backoff esponenziali. Se è necessario eseguire lo scaricamento sincrono, quindi `InMemoryChannel` è consigliato.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Canale di Application Insights offre recapito garantito di dati di telemetria o quali sono gli scenari in cui i dati di telemetria possono essere persi?*

* Risposta breve è che nessuno dei canali incorporati garantiscono transazione tipo sul recapito di dati di telemetria al back-end. Sebbene `ServerTelemetryChannel` è più avanzata rispetto ai `InMemoryChannel` per il recapito dei dati di telemetria affidabili anche effettua un tentativo migliore sforzo per inviare dati di telemetria e dati di telemetria possono comunque andranno persi in diversi scenari. Alcuni degli scenari comuni in cui i dati di telemetria viene perso:

1. Elementi nella cache andranno persi ogni volta che l'arresto anomalo dell'applicazione.
1. I dati di telemetria viene memorizzato nel disco locale durante le interruzioni di rete o problemi con il back-end di Application Insights. Tuttavia, vengono rimossi elementi più vecchi di 24 ore. Pertanto, i dati di telemetria viene persa durante un periodo prolungato di problemi di rete.
1. I percorsi di disco predefinito per l'archiviazione dei dati di telemetria in Windows sono % LocalAppData % o % Temp %. Questi percorsi vengono in genere locali nel computer. Se l'applicazione viene eseguita la migrazione fisica da una posizione a un'altra, i dati di telemetria archiviati in questa posizione viene perso.
1. Nelle App Web di Azure (Windows), il percorso del disco predefinito è "D:\local\LocalAppData". Questo percorso non è persistente e verrà cancellato in riavvii dell'app, scale-out e così via, causando una perdita di dati di telemetria archiviato in tali posizioni. Gli utenti possono eseguire l'override di archiviazione in un percorso persistente come "D:\home.", ma queste posizioni persistenti vengono visualizzati sotto gestite dal servizio di archiviazione remota e possono richiedere molto tempo.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel funziona nei sistemi non Windows?*

* Nonostante il nome del pacchetto/spazio dei nomi in Windows Server, questo canale è supportato nei sistemi non Windows con l'eccezione seguente. In non-Windows, il canale non crea una cartella di archiviazione locale per impostazione predefinita. Gli utenti devono creare una cartella di archiviazione locale e configurare il canale per usarlo. Dopo aver configurato l'archiviazione locale, il canale stesso funziona in Windows e sistemi non Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*il SDK Crea risorsa di archiviazione locale temporanea? I dati vengono crittografati nell'archiviazione?*

* SDK archivia gli elementi di telemetria nell'archiviazione locale durante i problemi di rete o la limitazione delle richieste. Questi dati non vengono crittografati in locale.
Per i sistemi Windows, il SDK automaticamente creata una cartella locale temporanea nella directory TEMP o APPDATA e limita l'accesso per gli amministratori e solo l'utente corrente.
Per Non-Windows, nessuna risorsa di archiviazione locale viene creato automaticamente dal SDK e pertanto non vengono archiviati dati localmente per impostazione predefinita. Gli utenti possono creare autonomamente una directory di archiviazione e configurare il canale per usarlo. In questo caso, l'utente è responsabile di assicurarsi che la directory è protetta.
Altre informazioni, vedere [privacy e protezione dei dati](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK open source
Ad esempio ogni SDK di Application Insights, i canali sono anche open source. Leggere e contribuire al codice o segnalare eventuali problemi [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Fasi successive

* [Campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi di SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
