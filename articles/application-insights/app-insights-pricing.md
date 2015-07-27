<properties 
	pageTitle="Gestire i prezzi e la quota per Application Insights" 
	description="Scegliere il piano tariffario necessario" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Gestire i prezzi e la quota per Application Insights

*Application Insights è disponibile in anteprima.*

I [prezzi][pricing] per [Application Insights di Visual Studio][start] si basano sul volume di dati per ogni applicazione. È anche disponibile un valido livello gratuito, che consente di usufruire di gran parte delle funzionalità, ma con alcune limitazioni.

Ogni risorsa di Application Insights viene addebitata come un servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure.

[Vedere lo schema dei prezzi][pricing].

## Esaminare la quota e il piano tariffario per la propria risorsa di Application Insights

È possibile aprire il pannello Quota + Prezzi dalle Impostazioni della risorsa dell'applicazione.

![Scegliere le impostazioni, la quota e i prezzi](./media/app-insights-pricing/01-pricing.png)

## Come funziona la quota?

* In ogni mese di calendario, l'applicazione può inviare fino a una quantità specificata di telemetria ad Application Insights. Per i numeri effetti, vedere lo [schema dei prezzi][pricing]. 
* La quota dipende dal piano tariffario scelto.
* La quota viene conteggiata da mezzanotte UTC il primo giorno di ogni mese.
* Il grafico Punti dati mostra la quantità di quota che è stata usata questo mese.
* La quota viene misurata in *punti dati*. Un singolo punto dati è una chiamata a uno dei metodi Track, se chiamato in modo esplicito nel codice o da uno dei moduli di telemetria standard. Ogni riga visibile nella ricerca di diagnostica è un punto dati. Ogni misurazione di una metrica, ad esempio un contatore delle prestazioni, è un punto dati. 
* I *dati della sessione* non vengono conteggiati nella quota. Ciò include il numero di utenti, sessioni, ambienti e dati del dispositivo.


## Eccedenza

Se l'applicazione invia una quantità superiore alla quota mensile, è possibile:

* Pagare i dati aggiuntivi. Per i dettagli, vedere lo [schema dei prezzi][pricing]. È possibile scegliere questa opzione in anticipo. Questa opzione non è disponibile nel piano tariffario Gratuito.
* Aggiornare il piano tariffario.
* Non eseguire alcuna operazione. I dati della sessione continueranno a essere registrati, ma altri dati non verranno visualizzati in ricerca diagnostica o in Esplora metriche.

## Versione di valutazione Premium gratuita

Quando si crea una nuova risorsa di Application Insights, viene avviata nel livello gratuito.

In qualsiasi momento, è possibile passare alla versione valutazione Premium gratuita di 30 giorni, che offre tutti i vantaggi del livello Premium. Dopo 30 giorni, verrà automaticamente ripristinato il livello precedente, a meno che non si scelga esplicitamente un altro livello. È possibile selezionare il livello desiderato in qualsiasi momento durante il periodo di valutazione, ma si otterrà comunque la versione di valutazione gratuita fino alla fine del periodo di 30 giorni.


## Come è stata usata la quota?

Il grafico nella parte inferiore del pannello dei prezzi mostra l'utilizzo dei punti dati dell'applicazione, raggruppati per tipo di punto dati.

![Nella parte inferiore del pannello dei prezzi](./media/app-insights-pricing/03-allocation.png)

Fare clic sul grafico per altri dettagli o trascinare gli elementi per visualizzare i dettagli relativi a un determinato intervallo di tempo.

## Esaminare la fattura per la sottoscrizione ad Azure

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).

![Nel menu laterale scegliere Fatturazione.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=July15_HO3-->