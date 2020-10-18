---
title: Monitorare Funzioni di Azure
description: Imparare a usare Azure Application Insights con Funzioni di Azure per monitorare l'esecuzione delle funzioni.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit
ms.openlocfilehash: bb6f2769ef675ac18fe8d2b58e17f9c10f457a6f
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164914"
---
# <a name="monitor-azure-functions"></a>Monitorare Funzioni di Azure

[Funzioni di Azure](functions-overview.md) offre l'integrazione predefinita con [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) per il monitoraggio delle funzioni. Questo articolo fornisce una panoramica delle funzionalità di monitoraggio fornite da Azure per il monitoraggio di funzioni di Azure.

Application Insights raccoglie i dati di log, delle prestazioni e degli errori. Grazie al rilevamento automatico delle anomalie delle prestazioni e alla presenza di potenti strumenti di analisi, è possibile diagnosticare più facilmente i problemi e comprendere meglio il modo in cui le funzioni vengono utilizzate. Questi strumenti sono progettati per contribuire a migliorare continuamente le prestazioni e l'usabilità delle funzioni. È anche possibile usare Application Insights durante lo sviluppo di un progetto locale di app per le funzioni. Per altre informazioni, vedere [Informazioni su Application Insights](../azure-monitor/app/app-insights-overview.md).

Poiché Application Insights strumentazione è incorporata in funzioni di Azure, è necessaria una chiave di strumentazione valida per connettere l'app per le funzioni a una risorsa Application Insights. La chiave di strumentazione viene aggiunta alle impostazioni dell'applicazione durante la creazione della risorsa dell'app per le funzioni in Azure. Se l'app per le funzioni non dispone già di questa chiave, è possibile [impostarla manualmente](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Prezzi e limiti di Application Insights

È possibile provare gratuitamente Application Insights integrazione con funzioni di Azure con un limite giornaliero per la quantità di dati elaborati gratuitamente.

Se si Abilita Application Insights durante lo sviluppo, è possibile raggiungere questo limite durante i test. Quando il limite giornaliero è quasi raggiunto, Azure invia notifiche tramite il portale e messaggi di posta elettronica. Se si ignorano gli avvisi e viene raggiunto il limite, i nuovi log non verranno visualizzati nelle query di Application Insights. È quindi importante prestare attenzione al limite per evitare di perdere tempo inutilmente. Per altre informazioni, vedere [Gestire volumi di dati e prezzi in Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights ha una funzionalità di [campionamento](../azure-monitor/app/sampling.md) che consente di evitare la produzione di un numero eccessivo di dati di telemetria sulle esecuzioni completate nei picchi di carico. Il campionamento è abilitato per impostazione predefinita. Se sembrano mancare dati, potrebbe essere semplicemente necessario regolare le impostazioni di campionamento in base allo specifico scenario di monitoraggio. Per altre informazioni, vedere [Configurare il campionamento](configure-monitoring.md#configure-sampling).

L'elenco completo delle funzionalità di Application Insights disponibili per le app per le funzioni è riportato in [Application Insights per le funzionalità supportate di Funzioni di Azure](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Integrazione di Application Insights

In genere, si crea un'istanza di Application Insights quando si crea l'app per le funzioni. In questo caso, la chiave di strumentazione necessaria per l'integrazione è già impostata come impostazione dell'applicazione denominata *APPINSIGHTS_INSTRUMENTATIONKEY*. Se per qualche motivo l'app per le funzioni non ha il set di chiavi di strumentazione, è necessario [abilitare l'integrazione Application Insights](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Raccolta di dati di telemetria

Con Application Insights integrazione abilitata, i dati di telemetria vengono inviati all'istanza di Application Insights connessa. Questi dati includono i log generati dall'host funzioni, le tracce scritte dal codice delle funzioni e i dati sulle prestazioni. 

>[!NOTE]
>Oltre ai dati delle funzioni e dell'host di funzioni, è anche possibile raccogliere dati dal controller di [scalabilità delle funzioni](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Livelli e categorie di log

Quando si scrivono tracce dal codice dell'applicazione, è necessario assegnare un livello di registrazione alle tracce. I livelli di registrazione consentono di limitare la quantità di dati raccolti dalle tracce.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Per altre informazioni sui livelli di registrazione, vedere [configurare i livelli di registrazione](configure-monitoring.md#configure-log-levels).

Assegnando gli elementi registrati a una categoria, si ha maggiore controllo sui dati di telemetria generati da origini specifiche nell'app per le funzioni. Le categorie semplificano l'esecuzione di analisi sui dati raccolti. Le tracce scritte dal codice della funzione vengono assegnate a singole categorie in base al nome della funzione. Per altre informazioni sulle categorie, vedere [configurare le categorie](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Dati di telemetria personalizzati

In [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) e [JavaScript](functions-reference-node.md#log-custom-telemetry)è possibile usare un SDK Application Insights per scrivere dati di telemetria personalizzati.

### <a name="dependencies"></a>Dependencies

A partire dalla versione 2. x di funzioni, il runtime raccoglie automaticamente i dati sulle dipendenze per le associazioni che usano alcuni SDK client. Application Insights raccoglie i dati sulle dipendenze seguenti:

+ Azure Cosmos DB 
+ Hub eventi di Azure
+ Bus di servizio di Azure
+ Servizi di archiviazione di Azure (BLOB, code e tabelle)

Vengono acquisite anche le richieste HTTP e le chiamate di database usando `SqlClient` . Per l'elenco completo delle dipendenze supportate da Application Insights, vedere [dipendenze rilevate automaticamente](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights genera una _mappa delle applicazioni_ dei dati di dipendenza raccolti. Di seguito è riportato un esempio di una mappa delle applicazioni di una funzione trigger HTTP con un'associazione di output di archiviazione di Accodamento.  

![Mappa delle applicazioni con dipendenza](./media/functions-monitoring/app-map.png)

Le dipendenze vengono scritte a `Information` livello. Se si applica un filtro a `Warning` o versione successiva, i dati delle dipendenze non verranno visualizzati. Inoltre, la raccolta automatica delle dipendenze si verifica in un ambito non utente. Per acquisire i dati delle dipendenze, verificare che il livello sia impostato almeno al di `Information` fuori dell'ambito utente ( `Function.<YOUR_FUNCTION_NAME>.User` ) nell'host.

Oltre alla raccolta automatica dei dati sulle dipendenze, è anche possibile usare uno degli SDK Application Insights specifici del linguaggio per scrivere le informazioni sulle dipendenze personalizzate nei log. Per un esempio di come scrivere dipendenze personalizzate, vedere uno degli esempi specifici del linguaggio seguenti:

+ [Registrare dati di telemetria personalizzati nelle funzioni C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Registrare dati di telemetria personalizzati nelle funzioni JavaScript](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Scrittura nei log 

La modalità di scrittura nei log e le API usate dipendono dalla lingua del progetto dell'app per le funzioni.   
Per ulteriori informazioni sulla scrittura dei log dalle funzioni, vedere la guida per gli sviluppatori della lingua.

+ [C# (libreria di classi .NET)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>Log in streaming

Durante lo sviluppo di un'applicazione, è spesso necessario vedere cosa viene scritto nei log quasi in tempo reale durante l'esecuzione in Azure.

Esistono due modi per visualizzare un flusso dei dati di log generati dalle esecuzioni di funzioni.

* **Streaming dei log predefinito**: la piattaforma del servizio app consente di visualizzare un flusso dei file di log dell'applicazione. Questo flusso è equivalente all'output visualizzato quando si esegue il debug delle funzioni durante [lo sviluppo locale](functions-develop-local.md) e quando si usa la scheda **test** nel portale. Vengono visualizzate tutte le informazioni basate sui log. Per altre informazioni, vedere [Eseguire lo streaming dei log](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Questo metodo di streaming supporta solo una singola istanza e non può essere usato con un'app in esecuzione su Linux con un piano a consumo.

* **Live Metrics Stream**: quando l'app per le funzioni è [connessa a Application Insights](configure-monitoring.md#enable-application-insights-integration), è possibile visualizzare i dati di log e altre metriche quasi in tempo reale nel portale di Azure usando [Live Metrics Stream](../azure-monitor/app/live-stream.md). È opportuno usare questo metodo quando si monitorano funzioni in esecuzione su più istanze o su Linux con un piano a consumo. Questo metodo usa [dati campionati](configure-monitoring.md#configure-sampling).

I flussi di log possono essere visualizzati sia nel portale sia nella maggior parte degli ambienti di sviluppo locali. Per informazioni su come abilitare i flussi di log, vedere [abilitare i log di esecuzione del flusso in funzioni di Azure](streaming-logs.md).

## <a name="diagnostic-logs"></a>Log di diagnostica

_Questa funzionalità è in anteprima._ 

Application Insights consente di esportare i dati di telemetria nell'archiviazione a lungo termine o in altri Analysis Services.  

Poiché le funzioni si integrano anche con monitoraggio di Azure, è anche possibile usare le impostazioni di diagnostica per inviare i dati di telemetria a diverse destinazioni, inclusi i log di monitoraggio di Azure. Per altre informazioni, vedere [Monitoraggio di Funzioni di Azure con i log di Monitoraggio di Azure](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Ridimensiona log del controller

_Questa funzionalità è in anteprima._ 

Il [controller di scalabilità di funzioni di Azure](./functions-scale.md#runtime-scaling) monitora le istanze dell'host di funzioni di Azure in cui viene eseguita l'app. Questo controller prende le decisioni relative al momento in cui aggiungere o rimuovere le istanze in base alle prestazioni correnti. È possibile fare in modo che il controller di scalabilità crei log per Application Insights per comprendere meglio le decisioni prese dal controller di scalabilità per l'app per le funzioni. È anche possibile archiviare i log generati nell'archivio BLOB per l'analisi da parte di un altro servizio. 

Per abilitare questa funzionalità, è necessario aggiungere un'impostazione dell'applicazione denominata `SCALE_CONTROLLER_LOGGING_ENABLED` per le impostazioni dell'app per le funzioni. Per informazioni, vedere [configurare i log del controller di scalabilità](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Segnalare i problemi

Per segnalare un problema con l'integrazione di Application Insights in Funzioni o per inviare un suggerimento o una richiesta, [creare un problema in GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Application Insights](/azure/application-insights/)
* [Registrazione di ASP.NET Core](/aspnet/core/fundamentals/logging/)
