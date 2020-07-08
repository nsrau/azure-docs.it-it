---
title: Note sulla versione per l'estensione app Web di Azure-Application Insights
description: Rilascia le note per l'estensione delle app Web di Azure per la strumentazione di runtime con Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 978cb7ece1f8b3e16815c1dc49e4983dc393cbfc
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937535"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Note sulla versione per l'estensione app Web di Azure per Application Insights

Questo articolo contiene le note sulla versione per l'estensione app Web di Azure per la strumentazione Runtime con Application Insights. Questa operazione è applicabile solo alle estensioni preinstallate.

[Altre informazioni](azure-web-apps.md) sull'estensione app Web di Azure per Application Insights.

## <a name="frequently-asked-questions"></a>Domande frequenti

- Come individuare la versione dell'estensione attualmente in corso
    - Passare a `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Per ulteriori informazioni, vedere [la guida dettagliata alla risoluzione dei problemi per il monitoraggio di estensioni/agenti](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net#troubleshooting) .

- Cosa accade se si usano le estensioni private?
    - Disinstallare le estensioni del sito privato poiché non è più supportato.

## <a name="release-notes"></a>Note sulla versione

### <a name="2833"></a>2.8.33

- .NET, .NET Core, Java e agenti Node.js e l'estensione Windows: supporto per i cloud sovrani. È possibile usare le stringhe di connessione per inviare dati ai cloud sovrani.

### <a name="2831"></a>2.8.31

- Agente ASP.NET Core: è stato risolto un problema relativo a uno dei riferimenti aggiornati di Application Insights SDK (vedere problemi noti per 2.8.26). Se la versione errata di `System.Diagnostics.DiagnosticSource.dll` è già caricata dal runtime, l'estensione senza codice ora non arresterà in modo anomalo l'applicazione ed eseguirà il backup. Per i clienti interessati da tale problema, è consigliabile rimuovere `System.Diagnostics.DiagnosticSource.dll` dalla cartella bin o usare la versione precedente dell'estensione impostando "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24". in caso contrario, il monitoraggio dell'applicazione non è abilitato.

### <a name="2826"></a>2.8.26

- Agente ASP.NET Core: risolto un problema relativo all'aggiornamento Application Insights SDK. `AiHostingStartup`Se la ApplicationInsights.dll è già presente nella cartella bin, l'agente non proverà a caricarlo. In questo modo vengono risolti i problemi correlati alla reflection tramite assembly \<AiHostingStartup\> . GetTypes ().
- Problemi noti: `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` è possibile che venga generata un'eccezione se è stata caricata un'altra versione della `DiagnosticSource` dll. Questo problema può verificarsi, ad esempio, se `System.Diagnostics.DiagnosticSource.dll` è presente nella cartella di pubblicazione. Come mitigazione, usare la versione precedente dell'estensione impostando le impostazioni dell'app nei servizi app: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- Versione riassemblata di 2.8.21.

### <a name="2823"></a>2.8.23

- Aggiunta del supporto per il monitoraggio con ASP.NET Core 3,0.
- Aggiornamento di ASP.NET Core SDK a [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) per le versioni di runtime 2,1, 2,2 e 3,0. Le app destinate a .NET Core 2,0 continuano a usare 2.1.1 dell'SDK.

### <a name="2814"></a>2.8.14

- Aggiornamento della versione di ASP.NET Core SDK da 2.3.0 alla versione più recente (2.6.1) per le app destinate a .NET Core 2,1, 2,2. Le app destinate a .NET Core 2,0 continuano a usare 2.1.1 dell'SDK.

### <a name="2812"></a>2.8.12

- Supporto per app ASP.NET Core 2,2.
- Correzione di un bug nell'estensione ASP.NET Core che causava l'inserimento dell'SDK anche quando l'applicazione è già instrumentata con l'SDK. Per le app 2,1 e 2,2, la presenza di ApplicationInsights.dll nella cartella dell'applicazione causa ora il backup dell'estensione. Per le app 2,0, l'estensione viene sottoposta a backup solo se ApplicationInsights è abilitato con una `UseApplicationInsights()` chiamata.

- Correzione permanente per la risposta HTML incompleta per le app ASP.NET Core. Questa correzione è ora estesa per funzionare per le app .NET Core 2,2.

- Aggiunta del supporto per disattivare l'inserimento JavaScript per le app ASP.NET Core ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). Per ASP.NET Core, per impostazione predefinita l'inserimento di JavaScript è in modalità "opt-out", a meno che non sia disattivato in modo esplicito. L'impostazione predefinita viene eseguita per mantenere il comportamento corrente.

- Correzione del bug di estensione del ASP.NET Core che causava l'inserimento anche se iKey non era presente.
- Correzione di un bug nella logica del prefisso della versione SDK che ha causato una versione non corretta dell'SDK nei dati di telemetria.

- Aggiunto il prefisso della versione SDK per le app ASP.NET Core per identificare la modalità di raccolta dei dati di telemetria.
- Correzione della pagina SCM-ApplicationInsights per visualizzare correttamente la versione dell'estensione preinstallata.

### <a name="2810"></a>2.8.10

- Correzione della risposta HTML incompleta per le app ASP.NET Core.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su come configurare il monitoraggio per i servizi app Azure, vedere la [documentazione del servizio app Azure](azure-web-apps.md) . 
