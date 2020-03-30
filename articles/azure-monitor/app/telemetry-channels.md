---
title: Canali di telemetria in Azure Application Insights . Documenti Microsoft
description: Come personalizzare i canali di telemetria negli SDK di Azure Application Insights per .NET e .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275698"
---
# <a name="telemetry-channels-in-application-insights"></a>Canali di telemetria in Application InsightsTelemetry channels in Application Insights

I canali di telemetria sono parte integrante degli SDK di [Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Gestiscono il buffering e la trasmissione dei dati di telemetria al servizio Application Insights. Le versioni .NET e .NET Core degli SDK dispongono `InMemoryChannel` di `ServerTelemetryChannel`due canali di telemetria incorporati: e . Questo articolo descrive in dettaglio ogni canale, incluso come personalizzare il comportamento del canale.

## <a name="what-are-telemetry-channels"></a>Che cosa sono i canali di telemetria?

I canali di telemetria sono responsabili dell'archiviazione nel buffer degli elementi di telemetria e dell'invio al servizio Application Insights, in cui vengono archiviati per l'esecuzione di query e l'analisi. Un canale di telemetria [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) è qualsiasi classe che implementa l'interfaccia.

Il `Send(ITelemetry item)` metodo di un canale di telemetria viene chiamato dopo la chiamata di tutti gli inizializzatori di telemetria e i processori di telemetria. Pertanto, tutti gli elementi rilasciati da un processore di telemetria non raggiungeranno il canale. `Send()`in genere non invia gli elementi al back-end istantaneamente. In genere, li memorizza nel buffer in memoria e li invia in batch, per una trasmissione efficiente.

[Live Metrics Stream](live-stream.md) ha anche un canale personalizzato che alimenta il live streaming dei dati di telemetria. Questo canale è indipendente dal canale di telemetria regolare e questo documento non si applica ad esso.

## <a name="built-in-telemetry-channels"></a>Canali di telemetria incorporati

Gli SDK .NET e .NET Core di Application Insights sono dotati di due canali predefiniti:

* `InMemoryChannel`: canale leggero che memorizza nel buffer gli elementi in memoria fino a quando non vengono inviati. Gli elementi vengono memorizzati nel buffer in memoria e scaricati una volta ogni 30 secondi o ogni volta che vengono memorizzati nel buffer 500 elementi. Questo canale offre garanzie di affidabilità minime perché non ritenta l'invio di dati di telemetria dopo un errore. Anche questo canale non mantiene gli elementi su disco, quindi tutti gli elementi non inviati vengono persi in modo permanente all'arresto dell'applicazione (gracefulo o meno). Questo canale `Flush()` implementa un metodo che può essere usato per forzare lo scaricamento di tutti gli elementi di telemetria in memoria in modo sincrono. Questo canale è adatto per applicazioni a esecuzione breve in cui uno svuotamento sincrono è l'ideale.

    Questo canale fa parte del pacchetto Microsoft.ApplicationInsights NuGet più grande ed è il canale predefinito utilizzato dall'SDK quando non è configurato nient'altro.

* `ServerTelemetryChannel`: un canale più avanzato con criteri di ripetizione dei tentativi e la possibilità di archiviare i dati su un disco locale. Questo canale tenta di inviare dati di telemetria se si verificano errori temporanei. Questo canale usa anche l'archiviazione su disco locale per mantenere gli elementi su disco durante le interruzioni di rete o volumi di telemetria elevati. A causa di questi meccanismi di ripetizione dei tentativi e dell'archiviazione su disco locale, questo canale è considerato più affidabile ed è consigliato per tutti gli scenari di produzione. Questo canale è l'impostazione predefinita per [le applicazioni ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e ASP.NET [Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) configurate in base alla documentazione ufficiale. Questo canale è ottimizzato per scenari server con processi a esecuzione prolungata. Il [`Flush()`](#which-channel-should-i-use) metodo implementato da questo canale non è sincrono.

    Questo canale viene fornito come pacchetto Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e viene acquisito automaticamente quando si utilizza Microsoft.ApplicationInsights.Web o Microsoft.ApplicationInsights.AspNetCore NuGet Pacchetto.

## <a name="configure-a-telemetry-channel"></a>Configurare un canale di telemetriaConfigure a telemetry channel

Per configurare un canale di telemetria, impostarlo sulla configurazione di telemetria attiva. Per le applicazioni ASP.NET, la configurazione `TelemetryConfiguration.Active`implica l'impostazione dell'istanza del canale di telemetria su o modificando `ApplicationInsights.config`. Per le applicazioni ASP.NET Core, la configurazione comporta l'aggiunta del canale al contenitore di inserimento delle dipendenze.

Nelle sezioni seguenti vengono illustrati `StorageFolder` esempi di configurazione dell'impostazione per il canale in vari tipi di applicazione. `StorageFolder`è solo una delle impostazioni configurabili. Per l'elenco completo delle impostazioni di configurazione, vedere [la sezione relativa](telemetry-channels.md#configurable-settings-in-channels) alle impostazioni più avanti in questo articolo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configurazione tramite ApplicationInsights.config per le applicazioni ASP.NET

La sezione seguente di ApplicationInsights.config `StorageFolder` mostra il canale configurato con impostato su un percorso personalizzato:The following section from [ApplicationInsights.config](configuration-with-applicationinsights-config.md) shows the `ServerTelemetryChannel` channel configured with set to a custom location:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Configurazione nel codice per le applicazioni di ASP.NET

Il codice seguente imposta un'istanza 'ServerTelemetryChannel' con `StorageFolder` impostato su un percorso personalizzato. Aggiungere questo codice all'inizio dell'applicazione, in genere nel `Application_Start()` metodo in Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configurazione nel codice per le applicazioni ASP.NET CoreConfiguration in code for ASP.NET Core applications

Modificare `ConfigureServices` il metodo `Startup.cs` della classe come illustrato di seguito:

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
> La configurazione del `TelemetryConfiguration.Active` canale tramite non è consigliata per ASP.NET applicazioni Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configurazione nel codice per le applicazioni console .NET/.NET CoreConfiguration in code for .NET/.NET Core console applications

Per le app console, il codice è lo stesso sia per .NET che per .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Dettagli operativi di ServerTelemetryChannelOperational details of ServerTelemetryChannel

`ServerTelemetryChannel`memorizza gli elementi in arrivo in un buffer in memoria. Gli elementi vengono serializzati, compressi `Transmission` e archiviati in un'istanza una volta ogni 30 secondi o quando 500 elementi sono stati memorizzati nel buffer. Una `Transmission` singola istanza contiene fino a 500 elementi e rappresenta un batch di dati di telemetria inviato tramite una singola chiamata HTTPS al servizio Application Insights.

Per impostazione predefinita, `Transmission` è possibile inviare un massimo di 10 istanze in parallelo. Se i dati di telemetria arrivano a velocità più elevate `Transmission` o se la rete o il back-end di Application Insights sono lenti, le istanze vengono archiviate in memoria. La capacità predefinita di `Transmission` questo buffer in memoria è 5 MB. Quando la capacità in memoria è `Transmission` stata superata, le istanze vengono archiviate su disco locale fino a un limite di 50 MB. `Transmission`le istanze vengono archiviate sul disco locale anche in caso di problemi di rete. Solo gli elementi archiviati su un disco locale sopravvivono a un arresto anomalo dell'applicazione. Vengono inviati ogni volta che l'applicazione ricomincia.

## <a name="configurable-settings-in-channels"></a>Impostazioni configurabili nei canali

Per l'elenco completo delle impostazioni configurabili per ogni canale, vedere:

* [Canale inMemoriaInMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [Canale ServerTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Qui ci sono le `ServerTelemetryChannel`impostazioni più comunemente utilizzate per :

1. `MaxTransmissionBufferCapacity`: la quantità massima di memoria, in byte, utilizzata dal canale per memorizzare nel buffer le trasmissioni in memoria. Quando viene raggiunta questa capacità, i nuovi elementi vengono archiviati direttamente sul disco locale. Il valore predefinito è 5 MB. L'impostazione di un valore più alto comporta un minore utilizzo del disco, ma tenere presente che gli elementi in memoria andranno persi se l'applicazione si arresta in modo anomalo.

1. `MaxTransmissionSenderCapacity`: il numero `Transmission` massimo di istanze che verranno inviate contemporaneamente ad Application Insights. Il valore predefinito è 10. Questa impostazione può essere configurata su un numero superiore, operazione consigliata quando viene generato un volume enorme di dati di telemetria. Un volume elevato si verifica in genere durante il test di carico o quando il campionamento è disattivato.

1. `StorageFolder`: la cartella utilizzata dal canale per memorizzare gli elementi su disco in base alle esigenze. In Windows, viene utilizzato %LOCALAPPDATA% o %TEMP% se non viene specificato in modo esplicito alcun altro percorso. In ambienti diversi da Windows, è necessario specificare un percorso valido o i dati di telemetria non verranno archiviati nel disco locale.

## <a name="which-channel-should-i-use"></a>Quale canale devo usare?

`ServerTelemetryChannel`è consigliato per la maggior parte degli scenari di produzione che coinvolgono applicazioni a esecuzione prolungata. Il `Flush()` metodo `ServerTelemetryChannel` implementato da non è sincrono e non garantisce l'invio di tutti gli elementi in sospeso dalla memoria o dal disco. Se si utilizza questo canale in scenari in cui l'applicazione sta per `Flush()`essere arrestata, è consigliabile introdurre un certo ritardo dopo la chiamata a . L'esatta quantità di ritardo che potrebbe essere necessaria non è prevedibile. Dipende da fattori come il `Transmission` numero di elementi o istanze in memoria, quanti sono su disco, quanti vengono trasmessi al back-end e se il canale è nel mezzo di scenari di back-off esponenziali.

Se è necessario eseguire uno svuotamento `InMemoryChannel`sincrono, è consigliabile utilizzare .

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Il canale Application Insights garantisce il recapito dei dati di telemetria? In caso contrario, quali sono gli scenari in cui è possibile perdere i dati di telemetria?

La risposta breve è che nessuno dei canali incorporati offre una garanzia di tipo di transazione del recapito dei dati di telemetria al back-end. `ServerTelemetryChannel`è più avanzato rispetto `InMemoryChannel` per un recapito affidabile, ma fa anche solo un tentativo ottimale di inviare dati di telemetria. La telemetria può comunque andare persa in diverse situazioni, inclusi gli scenari comuni:Telemetry can still be lost in several situations, including these common scenarios:

1. Gli elementi in memoria vengono persi quando l'applicazione si arresta in modo anomalo.

1. La telemetria viene persa durante lunghi periodi di problemi di rete. La telemetria viene archiviata sul disco locale durante le interruzioni di rete o quando si verificano problemi con il back-end di Application Insights.Telemetry is stored to local disk during network outages or when problems occur with the Application Insights back-end. Tuttavia, gli oggetti di età superiore alle 48 ore vengono scartati.

1. I percorsi predefiniti del disco per l'archiviazione dei dati di telemetria in Windows sono %LOCALAPPDATA% o %TEMP%. Queste posizioni sono in genere locali alla macchina. Se l'applicazione esegue la migrazione fisicamente da una posizione a un'altra, tutti i dati di telemetria archiviati nella posizione originale vengono persi.

1. Nelle app Web in Windows, il percorso predefinito di archiviazione su disco è D: Questo percorso non è persistente. Viene spazzato via nei riavvii delle app, nelle scalabilità orizzontali e in altre operazioni di questo tipo, causando la perdita di tutti i dati di telemetria archiviati in tale posizione. È possibile eseguire l'override dell'impostazione predefinita e specificare l'archiviazione in un percorso persistente, ad esempio D:. Tuttavia, tali posizioni persistenti sono servite da archiviazione remota e quindi possono essere lente.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel funziona su sistemi diversi da Windows?

Anche se il nome del pacchetto e dello spazio dei nomi include "WindowsServer", questo canale è supportato in sistemi diversi da Windows, con l'eccezione seguente. In sistemi diversi da Windows, il canale non crea una cartella di archiviazione locale per impostazione predefinita. È necessario creare una cartella di archiviazione locale e configurare il canale per utilizzarlo. Dopo aver configurato l'archiviazione locale, il canale funziona allo stesso modo su tutti i sistemi.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>L'SDK crea una risorsa di archiviazione locale temporanea? I dati vengono crittografati durante l'archiviazione?

L'SDK archivia gli elementi di telemetria nell'archiviazione locale durante i problemi di rete o durante la limitazione delle richieste. Questi dati non sono crittografati localmente.

Per i sistemi Windows, l'SDK crea automaticamente una cartella locale temporanea nella directory %TEMP% o %LOCALAPPDATA% e limita l'accesso agli amministratori e solo all'utente corrente.

Per i sistemi diversi da Windows, l'SDK non crea automaticamente alcuna risorsa di archiviazione locale e pertanto nessun dato viene archiviato localmente per impostazione predefinita. È possibile creare una directory di archiviazione manualmente e configurare il canale per utilizzarlo. In questo caso, l'utente è responsabile di garantire che la directory sia protetta.
Per saperne di più sulla [protezione dei dati e la privacy](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK open source
Come ogni SDK per Application Insights, i canali sono open source. Leggere e contribuire al codice, o segnalare problemi, presso [il repository ufficiale GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
