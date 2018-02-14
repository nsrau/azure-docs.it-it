---
title: Mappa delle applicazioni in Azure Application Insights | Microsoft Docs
description: Monitorare topologie di applicazioni complesse con la mappa delle applicazioni
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 3bbed59bf93eab5e729fbdd3ccae04599ac47081
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="application-map-triage-distributed-applications"></a>Mappa delle applicazioni: valutare le applicazioni distribuite
La mappa delle applicazioni consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti dell'applicazione distribuita. Ogni nodo nella mappa rappresenta un componente dell'applicazione o le relative dipendenze e ha un indicatore KPI dell'integrità e uno stato degli avvisi. È possibile fare clic da qualsiasi componente per ottenere una diagnostica più dettagliata, ad esempio sugli eventi di Application Insights. Se l'app usa i servizi di Azure, è possibile anche fare clic sulla diagnostica di Azure, ad esempio per consigli di Advisor su database SQL.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni. 

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. L'esperienza della mappa di anteprima mostra i componenti indipendentemente dalla loro configurazione.

## <a name="composite-application-map-preview"></a>Mappa delle applicazioni composita (anteprima)
*Questa mappa è in versione di anteprima e verranno aggiunte altre funzionalità. I commenti e i suggerimenti degli utenti relativi alla nuova esperienza saranno molto apprezzati. È possibile passare facilmente tra l'anteprima e l'esperienza classica.*

Abilitare la mappa delle applicazioni composita dall'[elenco delle anteprime](app-insights-previews.md) oppure fare clic sul pulsante relativo alla mappa di anteprima nell'angolo in alto a destra. Il pulsante consente anche di tornare all'esperienza classica.
![Abilitare la mappa di anteprima](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Questa versione di anteprima sostituisce la precedente anteprima della mappa delle applicazioni multiruolo. Per il momento, usare questa mappa per visualizzare l'intera topologia con più livelli di dipendenze dei componenti dell'applicazione. In base ai commenti e ai suggerimenti degli utenti verranno aggiunte ulteriori funzionalità analoghe a quelle supportate dalla mappa classica.

È possibile visualizzare la topologia completa dell'applicazione con più livelli di componenti dell'applicazione correlati. I componenti possono essere risorse di Application Insights diverse o ruoli diversi in una singola risorsa. La mappa delle app consente di trovare i componenti seguendo le chiamate di dipendenza HTTP inviate tra i server con Application Insights SDK installato. 

Questa esperienza inizia con la progressiva individuazione dei componenti. Quando si carica l'anteprima per la prima volta, viene avviato un set di query per individuare i componenti correlati a questo componente. Un pulsante nell'angolo superiore sinistro viene aggiornato con il numero di componenti dell'applicazione individuati. 
![Mappa di anteprima](media/app-insights-app-map/preview.png)

Quando si fa clic sul pulsante per aggiornare i componenti della mappa, la mappa viene aggiornata con tutti i componenti individuati fino a quel momento.
![Anteprima della mappa caricata](media/app-insights-app-map/components-loaded-hierarchical.png)

Se tutti i componenti sono ruoli all'interno di una singola risorsa di Application Insights, questo passaggio di individuazione non è necessario. Il caricamento iniziale per tale applicazione includerà tutti i relativi componenti.

Uno dei principali obiettivi della nuova esperienza è quello di consentire di visualizzare le topologie complesse con centinaia di componenti. La nuova esperienza supporta lo zoom e prevede l'aggiunta di dettagli quando si fa zoom avanti. È possibile fare zoom indietro per visualizzare contemporaneamente più componenti e individuare comunque i componenti con percentuali di errore maggiori. 

![Livelli di zoom](media/app-insights-app-map/zoom-levels.png)

Fare clic su qualsiasi componente per visualizzare le informazioni dettagliate correlate e passare all'esperienza di valutazione delle prestazioni e degli errori per il componente.

![Riquadro a comparsa](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Mappa delle applicazioni classica

La mappa mostra:

* Test della disponibilità
* Componente lato client (monitorato con JavaScript SDK)
* Componente lato server
* Dipendenze dei componenti client e server

![mappa delle app](./media/app-insights-app-map/02.png)

È possibile espandere e comprimere i gruppi di collegamento di dipendenza:

![comprimere](./media/app-insights-app-map/03.png)

Se si dispone di numerose dipendenze di un tipo (SQL, HTTP e così via), possono essere visualizzate raggruppate. 

![dipendenze raggruppate](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Individuazione di problemi
Ogni nodo dispone di indicatori di prestazioni rilevanti, ad esempio tassi di carico, prestazioni ed errori per il componente. 

Le icone di avviso evidenziano possibili problemi. Un avviso di colore arancione indica che si verificano errori nelle richieste, visualizzazioni di pagina o chiamate di dipendenza. Il rosso indica una percentuale di errore superiore al 5%. Se si vuole modificare queste soglie, aprire Opzioni.

![icone di errore](./media/app-insights-app-map/04.png)

Vengono visualizzati anche avvisi attivi: 

![avvisi attivi](./media/app-insights-app-map/05.png)

Se si usa SQL Azure, è presente un'icona che indica quando sono disponibili consigli su come è possibile migliorare le prestazioni. 

![Suggerimento di Azure](./media/app-insights-app-map/06.png)

Per ottenere altri dettagli, fare clic su qualsiasi icona:

![Suggerimento di Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Diagnostica tramite clic
Ciascun nodo della mappa permette una diagnostica tramite clic mirati. Le opzioni variano a seconda del tipo del nodo.

![opzioni server](./media/app-insights-app-map/09.png)

Per i componenti ospitati in Azure, le opzioni includono collegamenti diretti a essi.

## <a name="filters-and-time-range"></a>Filtri e intervallo di tempo
Per impostazione predefinita, la mappa riepiloga tutti i dati disponibili per l'intervallo di tempo scelto. è possibile filtrarlo in modo da includere solo i nomi di operazioni o dipendenze specifici.

* Nome dell'operazione: include sia le visualizzazioni pagina che i tipi di richiesta lato server. Con questa opzione, la mappa mostra l'indicatore KPI nel nodo lato server/client solo per le operazioni selezionate. Mostra le dipendenze chiamate nel contesto di tali operazioni specifiche.
* Nome di base delle dipendenze: include le dipendenze del browser AJAX e lato server. Se si segnalano dati di telemetria di dipendenza personalizzati con l'API TrackDependency, questi vengono visualizzati anche qui. È possibile selezionare le dipendenze da mostrare sulla mappa. Attualmente, questa selezione non filtra le richieste lato server o le visualizzazioni di pagina lato client.

![impostare i filtri](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Salvare i filtri
Per salvare i filtri applicati, bloccare la visualizzazione filtrata su un [dashboard](app-insights-dashboards.md).

![Aggiungi al dashboard](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Riquadro dell'errore
Quando si fa clic su un nodo nella mappa, viene visualizzato un riquadro dell'errore sul lato destro che riassume i problemi relativi a tale nodo. Gli errori vengono prima raggruppati per ID operazione e quindi per ID del problema.

![Riquadro dell'errore](./media/app-insights-app-map/error-pane.png)

Per passare all'istanza più recente dell'errore fare clic sull'errore stesso.

## <a name="resource-health"></a>Integrità delle risorse
Per alcuni tipi di risorsa, l'integrità delle risorse viene visualizzata nella parte superiore del riquadro dell'errore. Ad esempio, facendo clic su un nodo SQL verranno visualizzati l'integrità del database ed eventuali avvisi che sono stati attivati.

![Integrità delle risorse](./media/app-insights-app-map/resource-health.png)

È possibile fare clic sul nome della risorsa per visualizzare le metriche di panoramica standard per la risorsa.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Commenti e suggerimenti
Inviare commenti e suggerimenti tramite l'apposita opzione del portale.

![Immagine MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Passaggi successivi

* [Azure portal](https://portal.azure.com)
