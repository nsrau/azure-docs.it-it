<properties 
	pageTitle="Informazioni sui piani di hosting Web" 
	description="Monitoraggio del piano di hosting Web" 
	services="web-sites" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

# Informazioni sui piani di hosting Web #

I piani di hosting Web rappresentano un set di funzionalità e capacità che è possibile condividere tra tutti i siti Web in uso.  I piani di hosting Web supportano alcuni livelli di prezzo, ad esempio modalità gratuita, condivisa, base e standard, con ciascun livello che presenta funzionalità proprie.  I siti nella stessa sottoscrizione, gruppo di risorse e area geografica possono condividere un piano di hosting Web.

## Funzionalità dei piani di hosting Web ##

Ogni livello di prezzo (modalità gratuita, condivisa, base e standard) dispone del proprio set di funzionalità.  [Per informazioni aggiornate su funzionalità e prezzi, fare clic qui](http://go.microsoft.com/fwlink/?LinkID=394421).

Di seguito vengono fornite alcune informazioni utili in merito ai piani di hosting Web e alle relative funzionalità:

- È possibile modificare il livello di prezzo di un piano di hosting Web in qualsiasi momento con tempi di inattività pari a zero.
- I siti nella stessa sottoscrizione, area e gruppo di risorse possono condividere un piano di hosting Web. 
- Funzionalità come la scalabilità automatica funzionano con un piano di hosting Web come destinazione.  Se si desidera impostare la scalabilità automatica per un sito specifico, è necessario dedicare un piano di hosting Web a tale sito.

[Altre informazioni sulla gestione dei piani di hosting Web](http://go.microsoft.com/fwlink/?LinkID=394411).

## Piani di hosting Web e capacità ##

I piani di hosting Web per i livelli gratuito e condiviso forniscono siti con un'infrastruttura condivisa, ossia siti che condividono risorse con i siti di altri clienti.  

I piani di hosting Web per i livelli base e standard forniscono siti con un'infrastruttura dedicata, il che significa che solo i siti che si sceglie di associare al piano verranno eseguiti in quelle risorse.  Per questi livelli è possibile configurare il piano di hosting Web per usare una o più istanze di macchine virtuali.  Sono supportate istanze di dimensioni piccole, medie e grandi.  Tali macchine virtuali vengono gestite per conto del cliente, il che significa che non sarà necessario occuparsi di aggiornamenti del sistema operativo o di attività analoghe.  

Nota riguardante la modalità condivisa (in anteprima):  per tutti i livelli, ad eccezione del livello condiviso, viene applicato un prezzo unico per il piano di hosting Web in base al livello e alla capacità scelta. Non vi sono tariffe aggiuntive per ciascun sito che usa il piano. I piani di hosting Web condivisi sono diversi.  A causa della natura dell'infrastruttura condivisa, vengono applicati addebiti separati per ciascun sito nel piano.  

### Piani di hosting Web e il nuovo portale di anteprima di Azure ###

Il nuovo portale di anteprima di Azure consente di associare siti Web nuovi o esistenti a piani di hosting Web.  Tutti i siti Web sono stati infatti assegnati automaticamente a piani di hosting Web in base alla sottoscrizione, all'area geografica e all'attuale livello di prezzo.  

Quando si crea un nuovo sito, nel portale verrà richiesto a quale piano di hosting Web si desidera associarlo.  È possibile creare un nuovo piano di hosting Web o selezionarne uno esistente.  Per usare un piano esistente, il nuovo sito deve trovarsi nella stessa sottoscrizione, area geografica e gruppo di risorse in cui si trova tale piano esistente.  Per impostazione predefinita, quando si crea un nuovo sito Web vuoto, Azure proporrà il piano di hosting Web usato per ultimo per consentire un uso ottimale dei piani già creati.  Quando si crea un sito Web con un database, l'opzione di riutilizzare un piano esistente non sarà disponibile.

È possibile visualizzare tutti i piani di hosting Web per tutte le sottoscrizioni di cui si dispone usando il pulsante **Sfoglia** nella barra dei menu visualizzata a sinistra e quindi facendo clic su **Tutto** nella parte superiore destra del riquadro attività che viene visualizzato.

![][BrowseEverythingScreenshot]
![][BrowseWebHostingPlansScreenshot]

È inoltre possibile verificare a quale piano di hosting Web è associato ogni sito nella rappresentazione grafica del gruppo di risorse, visualizzata nella parte alta del pannello del sito Web.

![][WebHostingPlanResourceMapScreenshot]

Quando si fa clic sul piano verrà avviato un pannello che consente di gestire il piano di hosting Web.  [Altre informazioni sulla gestione dei piani di hosting Web](http://go.microsoft.com/fwlink/?LinkID=394411).

![][WebHostingPlanBladeScreenshot]
	

### Passaggi successivi ###

Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebHostingPlanResourceMapScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png
[WebHostingPlanBladeScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-blade.png
[BrowseWebHostingPlansScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-web-hosting-plans.png

<!--HONumber=46--> 

<!--HONumber=46--> 
