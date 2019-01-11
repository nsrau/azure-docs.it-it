---
title: Risolvere i problemi di riduzione del livello delle prestazioni - Servizio app di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni utili per la risoluzione dei rallentamenti delle prestazioni delle app nel Servizio app di Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: prestazioni dell'applicazione Web, app lenta, rallentamento app
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 790aab75b311b116e6ca03af016e181c11019e27
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726647"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Risoluzione dei problemi di rallentamento delle prestazioni delle app nel Servizio app di Azure
Questo articolo fornisce informazioni utili per la risoluzione dei rallentamenti delle prestazioni delle app nel [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito di supporto per Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## <a name="symptom"></a>Sintomo
Quando si esplora l'app, le pagine vengono caricate lentamente e a volte si verifica il timeout.

## <a name="cause"></a>Causa
Spesso la causa dell'errore deriva da problemi a livello dell'applicazione, ad esempio:

* le richieste di rete impiegano troppo tempo
* il codice dell'applicazione o le query di database non sono efficienti
* utilizzo elevato di memoria/CPU da parte dell'applicazione
* arresto anomalo dell'applicazione a causa di un'eccezione

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
La risoluzione dei problemi prevede tre attività distinte, in ordine sequenziale:

1. [Osservare e monitorare il comportamento dell'applicazione](#observe)
2. [Raccogliere i dati](#collect)
3. [Attenuare il problema](#mitigate)

Il [servizio app](overview.md) presenta diverse opzioni per ogni passaggio.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Osservare e monitorare il comportamento dell'applicazione
#### <a name="track-service-health"></a>Tenere traccia dell'integrità del servizio
Microsoft Azure pubblica un annuncio ogni volta che si verifica un'interruzione del servizio o una riduzione delle prestazioni. È possibile verificare l'integrità del servizio nel [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Tenere traccia dell’integrità del servizio](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorare l'app
Questa opzione consente di trovare eventuali problemi nell'applicazione. Nel pannello dell'app fare clic sul riquadro **Richieste ed errori**. Il pannello **Metrica** mostra tutte le metriche che si possono aggiungere.

Le metriche più comunemente monitorate per le app sono

* Working set della memoria medio
* Tempo di risposta medio
* Tempo CPU
* Working set della memoria
* Requests

![Monitorare le prestazioni dell'app](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Per altre informazioni, vedere:

* [Monitorare le app in Servizio app di Azure](web-sites-monitor.md)
* [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorare lo stato degli endpoint
Se si esegue l'app nel piano tariffario **Standard**, il servizio app consente di monitorare due endpoint da tre località geografiche.

Il monitoraggio degli endpoint configura i test Web da posizioni distribuite a livello geografico che testano il tempo di risposta e di attività degli URL Web. Il test esegue un'operazione HTTP GET sull'URL Web per determinare il tempo di risposta e di attività da ogni posizione. Ogni posizione configurata esegue un testo ogni cinque minuti.

Il tempo di attività viene monitorato mediante codici di risposta HTTP e il tempo di risposta è misurato in millisecondi. Un test di monitoraggio ha esito negativo se il codice della risposta HTTP è maggiore di o uguale a 400 o se la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se il test di monitoraggio ha esito positivo da tutte le posizioni specificate.

Per configurarlo, vedere [Eseguire il monitoraggio delle app nel servizio app di Azure](web-sites-monitor.md).

Per altre informazioni sul monitoraggio degli endpoint, vedere anche il video che illustra come [mantenere attivi i siti Web di Azure e monitorare gli endpoint con Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) .

#### <a name="application-performance-monitoring-using-extensions"></a>Monitoraggio delle prestazioni dell'applicazione usando le estensioni
È anche possibile monitorare le prestazioni dell'applicazione usando un'*estensione del sito*.

Ogni app del servizio app fornisce un endpoint di gestione estensibile che consente di usare un set avanzato di strumenti distribuiti come estensioni del sito. Le estensioni includono: 

- Editor di codice sorgente come [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Strumenti di gestione per le risorse connesse, ad esempio un database MySQL connesso a un'app.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) è un'altra estensione del sito disponibile per il monitoraggio delle prestazioni. Per usare Application Insights è necessario ricompilare il codice con un SDK. È anche possibile installare un'estensione che consente l'accesso a dati aggiuntivi. SDK consente di scrivere il codice per monitorare l'utilizzo e prestazioni dell'applicazione in modo più dettagliato. Per altre informazioni, vedere [Monitorare le prestazioni di applicazioni Web](../application-insights/app-insights-web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Raccogliere i dati
Il servizio app offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web, separate in diagnostica del server Web e diagnostica delle applicazioni.

#### <a name="enable-web-server-diagnostics"></a>Abilitare la diagnostica del server Web
È possibile abilitare o disabilitare i seguenti tipi di log:

* **Registrazione degli errori dettagliata**: consente di registrare informazioni dettagliate sugli errori relativi ai codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore), incluse eventualmente le informazioni che aiutano a determinare il motivo per cui il server ha restituito il codice di errore.
* **Traccia delle richieste non riuscita** : consente di registrare informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS utilizzati per elaborare la richieste e il tempo impiegato in ciascun componente. Ciò può essere utile se si sta provando a migliorare le prestazioni delle app o a isolare la causa di un errore HTTP specifico.
* **Registrazione del server Web** : consente di registrare informazioni sulle transazioni HTTP tramite il formato di file di log esteso W3C. Ciò è utile nel determinare le metriche generali dell'app, ad esempio il numero delle richieste gestite oppure quante di esse provengono da uno specifico indirizzo IP.

#### <a name="enable-application-diagnostics"></a>Abilitare la diagnostica delle applicazioni
Esistono diverse opzioni per raccogliere dati sulle prestazioni dell'applicazione dal servizio app, profilare l'applicazione in tempo reale da Visual Studio oppure modificare il codice dell'applicazione per registrare altre informazioni e tracce. È possibile scegliere le opzioni in base al livello di accesso consentito all'applicazione e ai dati osservati tramite gli strumenti di monitoraggio.

##### <a name="use-application-insights-profiler"></a>Usare Application Insights Profiler
È possibile abilitare Application Insights Profiler per avviare l'acquisizione di tracce dettagliate delle prestazioni. È possibile accedere alle tracce acquisite fino a cinque giorni prima, quando occorre analizzare i problemi che si sono verificati in passato. È possibile scegliere questa opzione, a condizione che sia disponibile l'accesso alla risorsa Application Insights dell'app nel portale di Azure.

Application Insights Profiler offre statistiche sui tempi di risposta per ogni chiamata Web e tracce che indicano la riga del codice che ha causato risposte lente. In alcuni casi l'app del servizio app è lenta perché una parte del codice non scritta in modo efficiente. È possibile ad esempio che siano presenti un codice sequenziale che può essere eseguito in parallelo e conflitti di blocco di database non previsti. La rimozione di questi colli di bottiglia nel codice migliora le prestazioni dell'app. Questi colli di bottiglia tuttavia risultano difficili da rilevare se non vengono configurati tracce e log elaborati. Le tracce raccolte da Application Insights Profiler sono utili per identificare le righe di codice che rallentano l'applicazione e consentono di risolvere questa problematica per le app del servizio app.

 Per altre informazioni, vedere [Profilare le app attive in Servizio app di Azure con Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Usare la profilatura remota
Nel Servizio app di Azure è possibile profilare in modalità remota app Web, app per le API, back-end per dispositivi mobili e processi Web. Scegliere questa opzione se si ha accesso alla risorsa app e si sa come riprodurre il problema o se si conosce l'intervallo di tempo esatto in cui si verifica il problema di prestazioni.

La profilatura remota è utile se l'utilizzo della CPU da parte del processo è elevato e l'esecuzione del processo è più lenta del previsto o se la latenza delle richieste HTTP è superiore al normale. In questi casi è possibile profilare il processo in modalità remota e ottenere campioni di stack di chiamate della CPU per analizzare l'attività del processo e i percorsi critici del codice.

Per altre informazioni, vedere [Remote Profiling support in Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service) (Supporto della profilatura remota in Servizio app di Azure).

##### <a name="set-up-diagnostic-traces-manually"></a>Configurare manualmente le tracce di diagnostica
Se si ha accesso al codice sorgente dell'applicazione Web, la diagnostica delle applicazioni consente di acquisire le informazioni prodotte da un'applicazione Web. Le applicazioni ASP.NET possono usare la classe `System.Diagnostics.Trace` per registrare le informazioni nel log di diagnostica applicazioni. È tuttavia necessario modificare il codice o ridistribuire l'applicazione. Questo metodo è consigliato se l'app è in esecuzione in un ambiente di testing.

Per istruzioni dettagliate su come configurare l'applicazione per la registrazione, vedere [Abilitare la registrazione diagnostica per le app nel Servizio app di Azure](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Usare lo strumento di diagnostica
Il servizio app offre un'esperienza dinamica e interattiva che consente di risolvere i problemi delle app senza ricorrere ad alcun intervento di configurazione. Quando vengono rilevati problemi nell'app, lo strumento di diagnostica ne evidenzia la natura per poter semplificare e velocizzare l'individuazione e la risoluzione del problema.

Per accedere alla diagnostica del servizio app, passare alla app del servizio app o all'ambiente del servizio app nel [portale di Azure](https://portal.azure.com). Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**.

#### <a name="use-the-kudu-debug-console"></a>Usare la console di debug Kudu
Il servizio app include una console di debug che è possibile usare per il debug, l'esplorazione e il caricamento di file, nonché endpoint JSON per ottenere informazioni sull'ambiente in uso. Questa console è chiamata *console Kudu* o *dashboard SCM* dell'app.

È possibile accedere a questo dashboard selezionando il collegamento **https://&lt;nome app>.scm.azurewebsites.net/**.

Elementi forniti dalla console Kudu:

* impostazioni di ambiente per l'applicazione
* log in streaming
* dump di diagnostica
* console di debug in cui è possibile eseguire cmdlet di PowerShell e comandi DOS di base.

Inoltre, nel caso in cui l'applicazione generi eccezioni first-chance, è possibile usare Kudu e l'utilità della riga di comando Procdump dello strumento SysInternals per creare dump della memoria. I dump della memoria sono snapshot del processo e semplificano la risoluzione di problemi più complessi riscontrati nell'app.

Per altre informazioni sulle funzionalità disponibili in Kudu, vedere gli [strumenti DevOps che è opportuno conoscere](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Attenuare il problema
#### <a name="scale-the-app"></a>Ridimensionare l'app
Nel servizio app di Azure, per ottimizzare le prestazioni e la velocità effettiva è possibile modificare la scalabilità in cui è in esecuzione l'applicazione. Aumentare le prestazioni di un'app implica due azioni correlate: passare a un piano tariffario superiore e configurare determinate impostazioni una volta adottato il nuovo piano.

Per altre informazioni sul ridimensionamento, vedere [Ridimensionare un'app nel Servizio app di Azure](web-sites-scale.md).

È anche possibile scegliere di eseguire l'applicazione in più di un'istanza. La scalabilità orizzontale non consente solo di ottenere una maggiore capacità di elaborazione, ma anche di usufruire di un certo livello di tolleranza di errore. Se il processo si arresta in un'istanza, le altre istanze continuano a gestire le richieste.

È possibile impostare il ridimensionamento manuale o automatico.

#### <a name="use-autoheal"></a>Usare la funzionalità AutoHeal
La funzionalità AutoHeal consente di riciclare il processo di lavoro per l'app in base alle impostazioni specificate, ad esempio modifiche di configurazione, richieste, limiti basati sulla memoria o il tempo necessario per l'esecuzione di una richiesta. Nella maggior parte dei casi, riciclare il processo costituisce il modo più veloce per risolvere un problema. Anche se è possibile riavviare l'app direttamente dall'interno del portale di Azure, la funzionalità AutoHeal esegue questa operazione automaticamente. È sufficiente aggiungere alcuni trigger nel file web.config radice per l'app. Queste impostazioni funzionano allo stesso modo anche per le app non .NET.

Per altre informazioni, vedere il post di blog relativo alla [correzione automatica di Siti Web di Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Riavviare l'app
Il riavvio è spesso il modo più semplice per risolvere problemi occasionali. Nel pannello dell'app del [portale di Azure](https://portal.azure.com/) sono disponibili le opzioni per arrestare o riavviare l'app.

 ![Riavviare l'app per risolvere i problemi di prestazioni](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

È anche possibile gestire l'app usando Azure PowerShell. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
