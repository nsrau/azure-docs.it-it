<properties 
	pageTitle="Mappa delle applicazioni in Application Insights | Microsoft Azure" 
	description="Una rappresentazione visiva delle dipendenze tra i componenti di app, contrassegnati con avvisi e indicatori KPI." 
	services="application-insights" 
    documentationCenter=""
	authors="SoubhagyaDash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="awills"/>
 
# Mappa delle applicazioni in Application Insights

In [Visual Studio Application Insights](app-insights-overview.md), Mappa delle applicazioni è un layout visivo delle relazioni di dipendenza dei componenti dell'applicazione. Ogni componente mostra gli indicatori KPI, ad esempio carico, prestazioni, errori e avvisi per individuare eventuali componenti che generano un errore o un problema di prestazioni. È possibile fare clic su qualsiasi componente per una diagnostica più dettagliata da Application Insights, e se l'app usa i servizi di Azure, dalla diagnostica di Microsoft Azure, ad esempio le indicazioni di Advisor per database SQL di Azure.

Come altri tipi di grafico, è possibile aggiungere una mappa delle applicazioni al dashboard di Azure, in cui è completamente funzionale.

## Aprire la mappa delle applicazioni

Aprire la mappa nel pannello di panoramica dell'applicazione:

![aprire la mappa delle app](./media/app-insights-app-map/01.png)

![mappa delle app](./media/app-insights-app-map/02.png)

La mappa mostra:

* Test della disponibilità
* Componente lato client (monitorato con JavaScript SDK)
* Componente lato server
* Dipendenze dei componenti client e server

È possibile espandere e comprimere i gruppi di collegamento di dipendenza:

![comprimere](./media/app-insights-app-map/03.png)
 
Se si dispone di un numero elevato di dipendenze di un tipo (SQL, HTTP e così via), possono essere visualizzate raggruppati.


![dipendenze raggruppate](./media/app-insights-app-map/03-2.png)
 
 
## Individuazione di problemi

Ogni nodo dispone di indicatori di prestazioni rilevanti, ad esempio i tassi di carico, le prestazioni e di errore per il componente.

Le icone di avviso evidenziano possibili problemi. Un avviso di colore arancione indica che si verificano errori nelle richieste, visualizzazioni di pagina o chiamate di dipendenza. Il rosso indica una percentuale di errore superiore al 5%.


![icone di errore](./media/app-insights-app-map/04.png)

 
Vengono visualizzati anche avvisi attivi:


![avvisi attivi](./media/app-insights-app-map/05.png)
 
Se si usa SQL Azure, è presente un'icona che indica quando sono disponibili consigli su come è possibile migliorare le prestazioni.


![Suggerimento di Azure](./media/app-insights-app-map/06.png)

Per ottenere altri dettagli, fare clic su qualsiasi icona:


![suggerimento di Azure](./media/app-insights-app-map/07.png)
 
 
## Diagnostica tramite clic

Ciascun nodo della mappa permette una diagnostica tramite clic mirati. Le opzioni variano a seconda del tipo del nodo.

![opzioni server](./media/app-insights-app-map/09.png)

 
Per i componenti ospitati in Azure, le opzioni includono collegamenti diretti a essi.


## Filtri e intervallo di tempo

Per impostazione predefinita, la mappa riepiloga tutti i dati disponibili per l'intervallo di tempo scelto. è possibile filtrarlo in modo da includere solo i nomi di operazioni o dipendenze specifici.

* Nome dell'operazione: include sia le visualizzazioni pagina sia i tipi di richiesta sul lato server. Con questa opzione, la mappa mostra solo l'indicatore KPI nel nodo sul lato server/client per le operazioni selezionate. Mostra le dipendenze chiamate nel contesto di tali operazioni specifiche.
* Nome di base delle dipendenze: include le dipendenze dal lato browser di AJAX e dal lato server. Se si segnalano dati di telemetria di dipendenza personalizzati con l'API TrackDependency, questi verranno visualizzati anche qui. È possibile selezionare le dipendenze da mostrare sulla mappa. Si noti che in questo momento, questo non filtrerà le richieste sul lato server o le visualizzazioni di pagina sul lato client.


![impostare i filtri](./media/app-insights-app-map/11.png)

 
 
## Salvare i filtri

Per salvare i filtri applicati, bloccare la visualizzazione filtrata su un [dashboard](app-insights-dashboards.md).


![Aggiungi al dashboard](./media/app-insights-app-map/12.png)
 


## Commenti e suggerimenti

[Fornire un feedback tramite l'opzione di feedback del portale](app-insights-get-dev-support.md).


![Immagine MapLink-1](./media/app-insights-app-map/13.png)

<!---HONumber=AcomDC_0622_2016-->