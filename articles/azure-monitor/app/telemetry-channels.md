---
title: Canali di telemetria in applicazione Azure Insights | Microsoft Docs
description: Come personalizzare i canali di telemetria in applicazione Azure Insights SDK per .NET e .NET Core.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cb7b9047e1036a2ab4bfd94cca88589dcdcd0ca3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899560"
---
# <a name="telemetry-channels-in-application-insights"></a>Canali di telemetria in Application Insights

I canali di telemetria sono parte integrante di [applicazione Azure SDK di Insights](../../azure-monitor/app/app-insights-overview.md). Gestiscono il buffering e la trasmissione dei dati di telemetria al servizio Application Insights. Le versioni .NET e .NET Core degli SDK includono due canali di telemetria predefiniti: `InMemoryChannel` e `ServerTelemetryChannel`. Questo articolo descrive in dettaglio ogni canale, inclusa la modalità di personalizzazione del comportamento del canale.

## <a name="what-are-telemetry-channels"></a>Che cosa sono i canali di telemetria?

I canali di telemetria sono responsabili del buffering degli elementi di telemetria e dell'invio al servizio Application Insights, dove vengono archiviati per l'esecuzione di query e l'analisi. Un canale di telemetria è qualsiasi classe che implementa l'interfaccia [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) .

Il metodo `Send(ITelemetry item)` di un canale di telemetria viene chiamato dopo la chiamata di tutti gli inizializzatori di telemetria e i processori di telemetria. Quindi, tutti gli elementi eliminati da un processore di telemetria non raggiungeranno il canale. `Send()` in genere non invia immediatamente gli elementi al back-end. In genere, li memorizza nel buffer in memoria e li invia in batch per una trasmissione efficiente.

[Live Metrics Stream](live-stream.md) dispone anche di un canale personalizzato che alimenta lo streaming live dei dati di telemetria. Questo canale è indipendente dal canale di telemetria normale e questo documento non è applicabile.

## <a name="built-in-telemetry-channels"></a>Canali di telemetria incorporati

Gli SDK Application Insights .NET e .NET Core sono forniti con due canali predefiniti:

* `InMemoryChannel`: un canale leggero che memorizza nel buffer gli elementi in memoria fino a quando non vengono inviati. Gli elementi vengono memorizzati nel buffer in memoria e scaricati ogni 30 secondi o ogni 500 elementi memorizzati nel buffer. Questo canale offre garanzie di affidabilità minime perché non ritenta l'invio di dati di telemetria dopo un errore. Anche questo canale non mantiene gli elementi su disco, pertanto tutti gli elementi non inviati andranno persi in modo permanente all'arresto dell'applicazione (normale o meno). Questo canale implementa un metodo di `Flush()` che può essere usato per forzare lo scaricamento di tutti gli elementi di telemetria in memoria in modo sincrono. Questo canale è particolarmente adatto per le applicazioni a esecuzione breve in cui uno scaricamento sincrono è ideale.

    Questo canale fa parte del pacchetto NuGet Microsoft. ApplicationInsights più grande ed è il canale predefinito usato dall'SDK quando non è configurato altro.

* `ServerTelemetryChannel`: un canale più avanzato con criteri di ripetizione dei tentativi e la capacità di archiviare i dati su un disco locale. Questo canale tenta di inviare dati di telemetria se si verificano errori temporanei. Questo canale usa anche l'archiviazione su disco locale per conservare gli elementi su disco durante le interruzioni di rete o volumi di telemetria elevati. Grazie a questi meccanismi di ripetizione dei tentativi e all'archiviazione su disco locale, questo canale viene considerato più affidabile ed è consigliato per tutti gli scenari di produzione. Questo canale è l'impostazione predefinita per le applicazioni [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) configurate in base alla documentazione ufficiale. Questo canale è ottimizzato per scenari server con processi a esecuzione prolungata. Il metodo [`Flush()`](#which-channel-should-i-use) implementato da questo canale non è sincrono.

    Questo canale viene fornito come pacchetto NuGet Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel e viene acquisito automaticamente quando si usa Microsoft. ApplicationInsights. Web o Microsoft. ApplicationInsights. AspNetCore NuGet pacchetto.

## <a name="configure-a-telemetry-channel"></a>Configurare un canale di telemetria

Per configurare un canale di telemetria, impostarlo sulla configurazione della telemetria attiva. Per le applicazioni ASP.NET, la configurazione prevede l'impostazione dell'istanza del canale di telemetria su `TelemetryConfiguration.Active`o la modifica di `ApplicationInsights.config`. Per ASP.NET Core applicazioni, la configurazione comporta l'aggiunta del canale al contenitore di inserimento delle dipendenze.

Le sezioni seguenti illustrano esempi di configurazione dell'impostazione `StorageFolder` per il canale in vari tipi di applicazioni. `StorageFolder` è solo una delle impostazioni configurabili. Per l'elenco completo delle impostazioni di configurazione, vedere [la sezione Impostazioni](telemetry-channels.md#configurable-settings-in-channels) più avanti in questo articolo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configurazione usando ApplicationInsights. config per le applicazioni ASP.NET

La sezione seguente di [ApplicationInsights. config](configuration-with-applicationinsights-config.md) Mostra il canale `ServerTelemetryChannel` configurato con `StorageFolder` impostato su un percorso personalizzato:

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

Il codice seguente configura un'istanza di ' ServerTelemetryChannel ' con `StorageFolder` impostato su un percorso personalizzato. Aggiungere questo codice all'inizio dell'applicazione, in genere in `Application_Start()` metodo in Global.aspx.cs.

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

Modificare il metodo `ConfigureServices` della classe `Startup.cs`, come illustrato di seguito:

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
> La configurazione del canale tramite `TelemetryConfiguration.Active` non è consigliata per le applicazioni ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configurazione nel codice per le applicazioni console .NET/.NET Core

Per le app console, il codice è lo stesso sia per .NET che per .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Dettagli operativi di ServerTelemetryChannel

`ServerTelemetryChannel` archivia gli elementi in arrivo in un buffer in memoria. Gli elementi vengono serializzati, compressi e archiviati in un'istanza di `Transmission` una volta ogni 30 secondi o quando 500 elementi sono stati memorizzati nel buffer. Una singola istanza di `Transmission` contiene fino a 500 elementi e rappresenta un batch di dati di telemetria inviati tramite una singola chiamata HTTPS al servizio Application Insights.

Per impostazione predefinita, è possibile inviare in parallelo un massimo di 10 istanze `Transmission`. Se i dati di telemetria arrivano a tariffe più veloci o se la rete o il Application Insights back-end è lento, le istanze di `Transmission` vengono archiviate in memoria. La capacità predefinita di questo buffer `Transmission` in memoria è 5 MB. Quando è stata superata la capacità in memoria, le istanze di `Transmission` vengono archiviate nel disco locale fino a un limite di 50 MB. le istanze di `Transmission` vengono archiviate nel disco locale anche quando si verificano problemi di rete. Solo gli elementi archiviati in un disco locale sopravvivono a un arresto anomalo dell'applicazione. Vengono inviati ogni volta che l'applicazione viene riavviata.

## <a name="configurable-settings-in-channels"></a>Impostazioni configurabili nei canali

Per l'elenco completo delle impostazioni configurabili per ogni canale, vedere:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Di seguito sono riportate le impostazioni usate più di frequente per `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: quantità massima di memoria, in byte, utilizzata dal canale per memorizzare nel buffer le trasmissioni in memoria. Quando viene raggiunta questa capacità, i nuovi elementi vengono archiviati direttamente nel disco locale. Il valore predefinito è 5 MB. L'impostazione di un valore più elevato comporta un minor utilizzo del disco, ma tenere presente che gli elementi in memoria andranno persi in caso di arresto anomalo dell'applicazione.

1. `MaxTransmissionSenderCapacity`: numero massimo di istanze di `Transmission` che verranno inviate al Application Insights nello stesso momento. Il valore predefinito è 10. Questa impostazione può essere configurata su un numero più elevato, che è consigliabile quando viene generato un volume elevato di dati di telemetria. Un volume elevato si verifica generalmente durante i test di carico o quando il campionamento è disattivato.

1. `StorageFolder`: cartella utilizzata dal canale per archiviare gli elementi su disco in base alle esigenze. In Windows, viene usato% LOCALAPPDATA% o% TEMP% se nessun altro percorso è specificato in modo esplicito. In ambienti diversi da Windows, è necessario specificare un percorso valido o i dati di telemetria non verranno archiviati nel disco locale.

## <a name="which-channel-should-i-use"></a>Quale canale si deve usare?

`ServerTelemetryChannel` è consigliabile per la maggior parte degli scenari di produzione che coinvolgono applicazioni a esecuzione prolungata. Il metodo `Flush()` implementato da `ServerTelemetryChannel` non è sincrono e non garantisce inoltre l'invio di tutti gli elementi in sospeso dalla memoria o dal disco. Se si utilizza questo canale negli scenari in cui l'applicazione sta per essere arrestata, è consigliabile introdurre un ritardo dopo la chiamata di `Flush()`. La quantità esatta di ritardo che potrebbe richiedere non è prevedibile. Dipende da fattori quali il numero di elementi o `Transmission` istanze presenti in memoria, il numero di dischi, il numero di volte trasmessi al back-end e il fatto che il canale si trovi nel mezzo di scenari di back-off esponenziali.

Se è necessario eseguire uno scaricamento sincrono, è consigliabile usare `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Il canale Application Insights garantisce il recapito dei dati di telemetria? In caso contrario, quali sono gli scenari in cui i dati di telemetria possono andare perduti?

La risposta breve è che nessuno dei canali predefiniti offre una garanzia del tipo di transazione per il recapito di dati di telemetria al back-end. `ServerTelemetryChannel` è più avanzato rispetto a `InMemoryChannel` per il recapito affidabile, ma esegue anche un tentativo di invio di dati di telemetria. I dati di telemetria possono comunque andare persi in diverse situazioni, inclusi questi scenari comuni:

1. Gli elementi in memoria vengono persi quando si verifica un arresto anomalo dell'applicazione.

1. I dati di telemetria vengono persi durante periodi prolungati di problemi di rete. I dati di telemetria vengono archiviati nel disco locale durante le interruzioni della rete o quando si verificano problemi con il Application Insights back-end. Tuttavia, gli elementi più vecchi di 24 ore vengono eliminati.

1. I percorsi dei dischi predefiniti per l'archiviazione dei dati di telemetria in Windows sono% LOCALAPPDATA% o% TEMP%. Questi percorsi sono in genere locali per il computer. Se l'applicazione esegue la migrazione fisicamente da una posizione a un'altra, tutti i dati di telemetria archiviati nel percorso originale andranno persi.

1. In app Web in Windows, il percorso predefinito di archiviazione su disco è D:\local\LocalAppData. Questo percorso non è permanente. Viene cancellato in riavvii dell'app, scale-out e altre operazioni di questo tipo, causando la perdita di dati di telemetria archiviati in tale posizione. È possibile eseguire l'override dell'impostazione predefinita e specificare lo spazio di archiviazione in una posizione permanente, ad esempio D:\HOME. Tuttavia, tali percorsi mantenuti vengono serviti dall'archiviazione remota e possono quindi essere lenti.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel funziona su sistemi diversi da Windows?

Sebbene il nome del pacchetto e dello spazio dei nomi includa "WindowsServer", questo canale è supportato in sistemi diversi da Windows, con la seguente eccezione. Nei sistemi diversi da Windows, per impostazione predefinita il canale non crea una cartella di archiviazione locale. È necessario creare una cartella di archiviazione locale e configurare il canale per usarlo. Dopo aver configurato l'archiviazione locale, il canale funziona allo stesso modo in tutti i sistemi.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>L'SDK crea una risorsa di archiviazione locale temporanea? I dati sono crittografati in archiviazione?

L'SDK archivia gli elementi di telemetria nell'archiviazione locale durante i problemi di rete o durante la limitazione delle richieste. Questi dati non vengono crittografati localmente.

Per i sistemi Windows, l'SDK crea automaticamente una cartella locale temporanea nella directory% TEMP% o% LOCALAPPDATA%, limitando l'accesso solo agli amministratori e all'utente corrente.

Per i sistemi diversi da Windows, l'SDK non crea automaticamente alcuna risorsa di archiviazione locale e pertanto nessun dato viene archiviato localmente per impostazione predefinita. È possibile creare manualmente una directory di archiviazione e configurare il canale per usarlo. In questo caso, l'utente è responsabile di garantire che la directory sia protetta.
Scopri di più sulla [protezione dei dati e sulla privacy](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK open source
Analogamente a ogni SDK per Application Insights, i canali sono open source. Leggere e contribuire al codice, o segnalare problemi, nel [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi di SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
