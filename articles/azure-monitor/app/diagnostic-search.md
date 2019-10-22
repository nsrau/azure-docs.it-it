---
title: Utilizzo della funzionalità Ricerca in Azure Application Insights | Microsoft Docs
description: Ricercare e filtrare elementi di telemetria non elaborata inviata da App Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/30/2019
ms.openlocfilehash: 77cd0a8d0c1a93e7dc1db931e987a172d31978ef
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678017"
---
# <a name="using-search-in-application-insights"></a>Utilizzo della funzionalità Ricerca in Application Insights

Ricerca è una funzionalità di [Application Insights](../../azure-monitor/app/app-insights-overview.md) che consente di trovare ed esplorare elementi singoli di telemetria, ad esempio visualizzazioni pagine, eccezioni o richieste Web. È possibile visualizzare le tracce del log e gli eventi codificati.

Per le query più complesse sui dati, utilizzare [Analytics](../../azure-monitor/log-query/get-started-portal.md).

## <a name="where-do-you-see-search"></a>Dove si trova Ricerca?

### <a name="in-the-azure-portal"></a>Nel portale di Azure

È possibile aprire la ricerca diagnostica dalla scheda Panoramica Application Insights dell'applicazione (che si trova nella barra superiore) o in analisi a sinistra.

![Scheda Cerca](./media/diagnostic-search/view-custom-events.png)

Passare al menu a discesa tipi di evento per visualizzare un elenco di elementi di telemetria: richieste server, visualizzazioni pagina, eventi personalizzati codificati e così via. Nella parte superiore dell'elenco dei risultati, è un grafico di riepilogo che mostra i conteggi degli eventi nel tempo.

Fare clic fuori dal menu a discesa o aggiornare per ottenere nuovi eventi.

### <a name="in-visual-studio"></a>In Visual Studio

In Visual Studio è inoltre disponibile una finestra Ricerca di Application Insights. È più utile per visualizzare gli eventi di telemetria generati dall'applicazione di cui si esegue il debug. È inoltre possibile visualizzare gli eventi raccolti dall'app pubblicata nel portale di Azure.

Aprire la finestra Cerca in Visual Studio:

![Funzionalità di ricerca di Application Insights aperta in Visual Studio](./media/diagnostic-search/32.png)

La finestra di ricerca ha funzionalità simili al portale Web:

![Finestra di ricerca di Application Insights in Visual Studio](./media/diagnostic-search/34.png)

La scheda Track Operation (Traccia operazione) è disponibile quando si apre una richiesta o una visualizzazione pagina. Con "operazione" si intende una sequenza di eventi associata a una singola richiesta o visualizzazione pagina. Ad esempio, chiamate di dipendenza, eccezioni, log di traccia ed eventi personalizzati potrebbero far parte di una singola operazione. La scheda Track Operation (Traccia operazione) mostra graficamente l'intervallo e la durata di questi eventi in relazione alla richiesta o alla visualizzazione pagina.

## <a name="inspect-individual-items"></a>Controllare i singoli elementi

Selezionare qualsiasi elemento di dati di telemetria per visualizzare i campi chiave e gli elementi correlati.

![Screenshot di una richiesta di dipendenza singola](./media/diagnostic-search/telemetry-item.png)

Verrà avviata la visualizzazione dei dettagli delle transazioni end-to-end.

## <a name="filter-event-types"></a>I tipi di eventi sono i seguenti:

Aprire il menu a discesa tipi di evento e scegliere i tipi di evento che si desidera visualizzare. Se, successivamente, si desidera ripristinare i filtri, fare clic su Reimposta.

I tipi di eventi sono i seguenti:

* **Traccia**:  - [log di diagnostica](../../azure-monitor/app/asp-net-trace-logs.md) con chiamate TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Richiesta**: richieste HTTP ricevute dall'applicazione server, tra cui pagine, script, immagini, file di stile e dati. Questi eventi vengono usati per creare grafici di panoramica di richieste e risposte.
* **Visualizzazione pagina**: - [i dati di telemetria inviati al client Web](../../azure-monitor/app/javascript.md), usati per creare report di visualizzazioni pagine.
* **Evento personalizzato**: se sono state inserite chiamate in TrackEvent() per [tenere traccia dell'utilizzo](../../azure-monitor/app/api-custom-events-metrics.md), è possibile cercarle qui.
* **Eccezione**: [eccezioni non rilevate nel server](../../azure-monitor/app/asp-net-exceptions.md) e quelle che si registrano con TrackException().
* **Dipendenza**:  - [chiamate dall'applicazione server](../../azure-monitor/app/asp-net-dependencies.md) ad altri servizi, ad esempio le API REST o i database, e chiamate AJAX dal [codice client](../../azure-monitor/app/javascript.md).
* **Disponibilità**: risultati dei [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrare in base ai valori delle proprietà

È possibile filtrare gli eventi in base ai valori delle relative proprietà. Le proprietà disponibili dipendono dai tipi di eventi selezionati. Fare clic sull'icona del filtro ![Icona filtro](./media/diagnostic-search/filter-icon.png) per iniziare.

La mancata scelta dei valori di una determinata proprietà ha lo stesso effetto della scelta di tutti i valori. Viene disattivata l'applicazione dei filtri per quella proprietà.

Si noti che il numero a destra dei valori di filtro mostra quante occorrenze sono incluse nel set filtrato corrente.

## <a name="find-events-with-the-same-property"></a>Trovare gli eventi con la stessa proprietà

Per trovare tutti gli elementi con lo stesso valore della proprietà, digitarli nella barra di ricerca oppure fare clic sulla casella di controllo quando si esaminano le proprietà nella scheda filtro.

![Fare clic sulla casella di controllo di una proprietà nella scheda filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Eseguire ricerche nei dati

> [!NOTE]
> Per scrivere query più complesse, aprire [**logs (Analytics)** ](../../azure-monitor/log-query/get-started-portal.md) nella parte superiore del pannello Search (Cerca).
>

È possibile cercare i termini in uno dei valori delle proprietà. Questa operazione è utile se sono stati scritti [eventi personalizzati](../../azure-monitor/app/api-custom-events-metrics.md) con valori di proprietà.

È possibile che si voglia impostare un intervallo di tempo, poiché le ricerche di un intervallo più breve sono più veloci.

![Aprire la ricerca diagnostica](./media/diagnostic-search/search-property.png)

Cercare parole complete, non sottostringhe. Utilizzare le virgolette per racchiudere i caratteri speciali.

| Stringa | *Non* trovato | Trovato |
| --- | --- | --- |
| ControllerHome.Info |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Stati Uniti|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

È possibile usare espressioni di ricerca quali le seguenti:

| Query di esempio | Effetto |
| --- | --- |
| `apple` |Individuazione di tutti gli eventi nell'intervallo di tempo i cui campi includono la parola "mela" |
| `apple AND banana` <br/>`apple banana` |Individuazione di eventi che contengono entrambe le parole. Usare "AND" in lettere maiuscole, non "and". <br/>Forma breve. |
| `apple OR banana` |Individuazione degli eventi che contengono una delle parole. Usare "OR", non "or". |
| `apple NOT banana` |Individua eventi che contengono una parola ma non l'altra. |

## <a name="sampling"></a>campionamento

Se l'app genera una grande quantità di dati di telemetria (e si usa ASP.NET SDK versione 2.0.0-beta3 o successiva), il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Tuttavia, gli eventi che fanno parte della stessa richiesta vengono selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati.

[Informazioni sul campionamento.](../../azure-monitor/app/sampling.md)

## <a name="create-work-item"></a>Creare un elemento di lavoro

È possibile creare un bug in GitHub o Azure DevOps con i dettagli provenienti da qualsiasi elemento di dati di telemetria.

Passare alla visualizzazione dettagli transazione end-to-end facendo clic su qualsiasi elemento di telemetria, quindi selezionare **Crea elemento di lavoro**.

![Fare clic su Nuovo elemento di lavoro, modificare i campi e quindi fare clic su OK.](./media/diagnostic-search/work-item.png)

La prima volta che si esegue questa operazione viene chiesto di configurare un collegamento all'organizzazione e al progetto di Azure DevOps.

È anche possibile configurare il collegamento nella scheda elementi di lavoro.

## <a name="send-more-telemetry-to-application-insights"></a>Inviare altri dati di telemetria ad Application Insights

Oltre la telemetria predefinita inviata da Application Insights SDK, è possibile:

* Acquisire le tracce del log dal framework di registrazione preferito in [.NET](../../azure-monitor/app/asp-net-trace-logs.md) o [Java](../../azure-monitor/app/java-trace-logs.md). Ciò significa che è possibile cercare le tracce del log e metterle in correlazione con le visualizzazioni pagina, le eccezioni e altri eventi.
* [Scrivere codice](../../azure-monitor/app/api-custom-events-metrics.md) per inviare eventi personalizzati, visualizzazioni pagina ed eccezioni.

[Informazioni su come inviare log e telemetria personalizzata ad Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>Domande e risposte

### <a name="limits"></a>Quanti dati vengono conservati?

Vedere il [Riepilogo dei limiti](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Come è possibile visualizzare dati POST nelle richieste server?

I dati POST non vengono registrati automaticamente, ma è possibile usare [TrackTrace o chiamate di log](../../azure-monitor/app/asp-net-trace-logs.md). Inserire i dati POST nel parametro del messaggio. Non è possibile filtrare in base al messaggio nello stesso modo delle proprietà, ma il limite delle dimensioni è maggiore.

## <a name="add"></a>Passaggi successivi

* [Scrivere query complesse in Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Inviare log e dati di telemetria personalizzati ad Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurare i test di disponibilità e velocità di risposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [risoluzione dei problemi](../../azure-monitor/app/troubleshoot-faq.md)
