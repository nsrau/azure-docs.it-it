---
title: Note sulla versione per Application Insights per Windows
description: Note sulla versione dell&quot;SDK (obsolete).
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 62115aba-f806-447b-9cca-76d0e11cfbd5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb
ROBOTS: NOINDEX,NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 8c5324742e42a1f82bb3031af4380fc5f0241d7f
ms.openlocfilehash: 6868024441e3e0e0d2fdc44499a9b1e00364b31e

---
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Note sulla versione per Application Insights SDK per Windows Phone e Store

> [!WARNING]
> L'SDk per Windows Phone e Store non è più supportato. È consigliabile usare [HockeyApp](https://www.hockeyapp.net/) per gestire la distribuzione e il monitoraggio delle app desktop e per dispositivi mobili.
>  

Application Insights SDK invia dati di telemetria sull'app live ad [Application Insights](https://azure.microsoft.com/services/application-insights/), in cui è possibile analizzarne l'utilizzo e le prestazioni.

## <a name="version-111"></a>Versione 1.1.1
### <a name="windows-sdk"></a>Windows SDK
* Correggere un blocco durante l'arresto anomalo quando si utilizza l’SDK per Windows Phone Silverlight. Dopo questa modifica, qualsiasi arresto anomalo che si verifica dopo ~ 2 secondi dalla chiamata a WindowsAppInitialier.InitializeAsync(...) verrà mantenuto nel disco e verrà inviato al successivo avvio dell'applicazione. Se si verifica un arresto anomalo prima di ~ 2 secondi dalla chiamata, verrà ignorato.  
* Impostare le dipendenze di NuGet su una versione specifica di Core e Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>SDK di base
* Core è gestito in github. Sono disponibili note sulla versione futura dell’SDK di base [in github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Versione 1.1
### <a name="core-sdk"></a>SDK di base
* SDK ora rende disponibile un nuovo tipo di telemetria ```DependencyTelemetry``` che contiene informazioni sulla dipendenza chiamata dall'applicazione
* Il nuovo metodo ```TelemetryClient.TrackDependency``` consente di inviare informazioni sulla dipendenza chiamata dall'applicazione

## <a name="version-100"></a>Versione 1.0.0
### <a name="windows-app-sdk"></a>Windows App SDK
* Nuova inizializzazione per le applicazioni Windows. La nuova classe `WindowsAppInitializer` con il metodo `InitializeAsync()` permette l'inizializzazione bootstrap della raccolta di SDK. Questa modifica offre un controllo più preciso e miglioramenti significativi alle prestazioni dell'inizializzazione dell'app rispetto alla tecnica precedente di ApplicationInsights.config.
* La modalità DeveloperMode non viene più impostata automaticamente. Per modificare il comportamento di DeveloperMode, è necessario specificarlo nel codice.
* Il pacchetto NuGet non inserisce più ApplicationInsights.config. È consigliabile usare il nuovo WindowsAppInitializer quando si aggiunge manualmente il pacchetto NuGet.
* ApplicationInsights.config legge solo `<InstrumentationKey>`. Tutte le altre impostazioni vengono ignorate a favore delle impostazioni di WindowsAppInitializer.
* Store Market verrà raccolto automaticamente dall'SDK.
* Molte correzioni di bug, miglioramenti alla stabilità e ottimizzazioni delle prestazioni.

### <a name="core-sdk"></a>SDK di base
* Il file ApplicationInsights.config non è più necessario e non viene aggiunto dal pacchetto NuGet. La configurazione può essere specificata completamente nel codice.
* Il pacchetto NuGet non aggiungerà più file di destinazione alla soluzione. Verrà quindi rimossa l'impostazione automatica di DeveloperMode durante una compilazione di debug. DeveloperMode deve essere impostato manualmente nel codice.

## <a name="version-017"></a>Versione 0.17
### <a name="windows-app-sdk"></a>Windows App SDK
* Windows App SDK supporta ora le app di Windows universali create nell'anteprima tecnica di Windows 10 e con VS 2015 RC.

### <a name="core-sdk"></a>SDK di base
* Il valore predefinito per TelemetryClient comporta l'inizializzazione con InMemoryChannel.
* È stata aggiunta una nuova API, `TelemetryClient.Flush()`. Questo metodo Flush attiverà un caricamento bloccante immediato di tutti i dati di telemetria registrati in tale client. Ciò permette l'attivazione manuale del caricamento prima dell'arresto del processo.
* Il pacchetto NuGet ha aggiunto una destinazione .NET 4.5. Questa destinazione non presenta dipendenze esterne. Le dipendenze BCL ed EventSource sono state rimosse.

## <a name="version-016"></a>Versione 0.16
Anteprima del 28/04/2015

* L'SDK supporta ora la piattaforma di destinazione DNX per consentire il monitoraggio delle applicazioni per [.NET Core Framework](http://www.dotnetfoundation.org/NETCore5) .
* Le istanze di ```TelemetryClient``` non memorizzano più nella cache la chiave di strumentazione. Ora, se la chiave di strumentazione non è stata impostata in modo esplicito in ```TelemetryClient```, ```InstrumentationKey``` restituirà Null. In questo modo viene risolto un problema per cui quando si imposta ```TelemetryConfiguration.Active.InstrumentationKey``` dopo che sono già stati raccolti alcuni dati di telemetria, i moduli di telemetria come agente di raccolta dipendenze, raccolta dati delle richieste Web e agente di raccolta contatori delle prestazioni useranno una nuova chiave di strumentazione.

## <a name="version-015"></a>Versione 0.15
* La nuova proprietà ```Operation.SyntheticSource``` è ora disponibile in ```TelemetryContext```. Ora è possibile contrassegnare gli elementi di telemetria come "traffico utente non reale" e specificare il modo in cui il traffico è stato generato. Ad esempio impostando questa proprietà è possibile distinguere il traffico che deriva dall'automazione dei test da quello che deriva dal test di carico.
* La logica di canale è stata spostata in un altro pacchetto NuGet denominato Microsoft.ApplicationInsights.PersistenceChannel. Il canale predefinito è ora denominato InMemoryChannel
* Il nuovo metodo ```TelemetryClient.Flush``` consente di scaricare gli elementi di telemetria dal buffer in modo sincrono

## <a name="version-013"></a>Versione 0.13
Per le versioni precedenti non sono disponibili le note sulla versione. 




<!--HONumber=Nov16_HO4-->


