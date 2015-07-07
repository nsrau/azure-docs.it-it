<properties 
	pageTitle="Creare una nuova risorsa di Application Insights" 
	description="Configurare il monitoraggio di una nuova applicazione e ottenere una nuova chiave di strumentazione. Application Insights monitora prestazioni e utilizzo delle applicazioni attive." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="awills"/>

# Creare una nuova risorsa di Application Insights



[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights di Visual Studio mostra dati relativi all'applicazione in una *risorsa* di Microsoft Azure. La creazione di una nuova risorsa fa dunque parte della [configurazione di Application Insights per monitorare una nuova applicazione][start]. In molti casi, questa operazione può essere eseguita automaticamente dall'IDE, cioè la modalità consigliata in cui è disponibile. In alcuni casi, tuttavia, è necessario creare una risorsa manualmente.

Dopo aver creato la risorsa, si ottiene la relativa chiave di strumentazione, che consente di configurare l'SDK nell'applicazione. In tal modo, verranno inviati dati di telemetria alla risorsa.

## Iscrizione a Microsoft Azure

Se non si ha ancora un [account Microsoft, crearne uno ora](http://live.com) (se si usano servizi come Outlook.com, OneDrive, Windows Phone o XBox Live, si ha già un account Microsoft).

È necessaria anche una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando Windows Live ID.

In alternativa, è possibile creare una nuova sottoscrizione. La versione di valutazione gratuita consente di provare tutto in Azure. Alla scadenza del periodo di valutazione, l'utente potrebbe trovare appropriata la sottoscrizione con pagamento in base al consumo, perché non si incorre in alcun addebito per i servizi gratuiti.

Dopo aver ottenuto una sottoscrizione, accedere ad Application Insights all'indirizzo [http://portal.azure.com](http://portal.azure.com) usando il proprio Live ID.


## Creare una risorsa Application Insights
  

In [portal.azure.com](https://portal.azure.com), aggiungere una nuova risorsa di Application Insights:

![Fare clic su Nuovo, Application Insights](./media/app-insights-create-new-resource/01-new.png)


* Il **tipo di applicazione** influisce sul contenuto del pannello Panoramica e sulle proprietà disponibili nell'[area di esplorazione delle metriche][metrics]. Se non viene visualizzato il proprio tipo di app, scegliere uno dei tipi Web per le pagine Web e uno dei tipi telefono per gli altri dispositivi.
* Un **gruppo di risorse** è utile per gestire le proprietà come il controllo di accesso. Se sono già state create altre risorse di Azure, è possibile inserire questa nuova risorsa nello stesso gruppo.
* La **sottoscrizione** è il proprio account di pagamento in Azure.
* Il **percorso** è il luogo in cui sono conservati i propri dati, attualmente non modificabile.
* L'opzione **Aggiungi a schermata iniziale** inserisce un riquadro di accesso rapido alla propria risorsa nella pagina iniziale di Azure. Consigliato.

Dopo aver creato l'app, viene visualizzato un nuovo pannello che mostra le prestazioni e i dati di utilizzo relativi all'app.

Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, cercare il riquadro di avvio rapido dell'app nella schermata iniziale. In alternativa, fare clic su Sfoglia per cercarlo.


## Eseguire una copia della chiave di strumentazione.


Sarà necessaria a breve per indirizzare i dati dall'SDK nell'app alla risorsa appena creata.

![Fare clic su Proprietà, selezionare il tasto e premere Ctrl+C](./media/app-insights-create-new-resource/02-props.png)

## Configurare l'SDK

Usare la chiave di strumentazione per configurare [l'SDK installato nell'applicazione][start].

Questo passaggio dipende dal tipo di applicazione che si sta usando.

In alcuni casi, è necessario installare i moduli standard che inviano dati di telemetria senza dover scrivere alcun codice. In ogni caso, è possibile [usare l'API][api] per inviare i propri dati di telemetria.

## <a name="monitor"></a>Vedere dati di telemetria

Chiudere il pannello di avvio rapido per tornare al pannello dell'applicazione nel portale di Azure.

Fare clic sul riquadro Cerca per vedere [Ricerca diagnostica][diagnostic], ossia la finestra in cui vengono visualizzati i primi eventi.

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->