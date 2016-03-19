<properties
	pageTitle="Risoluzione dei problemi: App Web non disponibile a causa dei codici di errore HTTP 502/503"
	description="Questo articolo descrive come risolvere gli errori di tipo HTTP 502/503 nell'app Web ospitata nel servizio app di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="cephalin"/>

# Risoluzione dei problemi: App Web non disponibile a causa dei codici di errore HTTP 502/503

Questo articolo descrive come risolvere gli errori di tipo HTTP 502/503 nell'app Web ospitata nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow dedicati ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito di supporto per Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## Sintomo

Quando si passa all'app Web, viene restituito un errore HTTP "502 - Gateway non valido" o un errore HTTP "503 - Servizio non disponibile".

## Causa

Spesso la causa dell'errore deriva da problemi a livello dell'applicazione, ad esempio:

-	le richieste impiegano troppo tempo
-	utilizzo elevato di memoria/CPU da parte dell'applicazione
-	arresto anomalo dell'applicazione a causa di un'eccezione.

## Passaggi per la risoluzione dei problemi

La risoluzione dei problemi prevede tre attività distinte, in ordine sequenziale:

1.	[Osservare e monitorare il comportamento dell'applicazione](#observe)
2.	[Raccogliere i dati](#collect)
3.	[Attenuare il problema](#mitigate)

In [app Web del servizio app](/services/app-service/web/) vengono presentate diverse opzioni per ogni passaggio.

<a name="observe" />
### 1\. Osservare e monitorare il comportamento dell'applicazione

####	Tenere traccia dell'integrità del servizio

Microsoft Azure pubblica un annuncio ogni volta che si verifica un'interruzione del servizio o una riduzione delle prestazioni. È possibile verificare l'integrità del servizio nel [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Tenere traccia dell'integrità del servizio](../azure-portal/insights-service-health.md).

####	Monitorare l'app Web

Questa opzione consente di trovare eventuali problemi nell'applicazione. Nel pannello dell'app Web fare clic sul riquadro **Richieste ed errori**. Il pannello **Metrica** mostra le metriche che si possono aggiungere.

Le metriche più comunemente monitorate per le app Web sono

-	Working set della memoria medio
-	Tempo medio di risposta
-	Tempo CPU
-	Working set della memoria
-	Richieste

![](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Per altre informazioni, vedere:

-	[Eseguire il monitoraggio delle app Web nel servizio app di Azure](web-sites-monitor.md)
-	[Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md)

<a name="collect" />
### 2\. Raccogliere i dati

####	Usare il portale di supporto del servizio app di Azure

Il servizio app Web consente di risolvere i problemi relativi all'app Web grazie ai dati disponibili nei log HTTP, nei log eventi, nei dump dei processi e così via. È possibile accedere a tutte queste informazioni tramite il portale di supporto disponibile all'indirizzo **http://&lt;your nome app>.scm.azurewebsites.net/Support**

Il portale di supporto del servizio app di Azure contiene tre schede separate per supportare i tre passaggi di uno scenario di risoluzione dei problemi comune:

1.	Osservare il comportamento corrente
2.	Eseguire l'analisi tramite la raccolta di informazioni di diagnostica e l'esecuzione di analizzatori predefiniti
3.	Attenuare il problema

Se si riscontra il problema in questo preciso istante, fare clic su **Analizza** > **Diagnostica** > **Esegui diagnosi adesso** per creare una sessione di diagnostica in cui verranno raccolti log HTTP, log del visualizzatore eventi, dump della memoria, log degli errori PHP e report sui processi PHP.

Una volta raccolti i dati, verrà eseguita l'analisi dei dati e verrà generato un report HTML.

Per impostazione predefinita, i dati verranno archiviati nella cartella D:\\home\\data\\DaaS, da cui sarà possibile scaricarli.

Per altre informazioni sul portale di supporto del servizio app di Azure, vedere il post di blog relativo ai [nuovi aggiornamenti all'estensione del sito di supporto per Siti Web di Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####	Usare la console di debug Kudu

Il servizio App Web include una console di debug che è possibile usare per il debug, l'esplorazione e il caricamento di file, nonché endpoint JSON per ottenere informazioni sull'ambiente in uso. Questa console è chiamata _console Kudu_ o _dashboard SCM_ dell'app Web.

È possibile accedere a questo dashboard selezionando il collegamento **https://&lt;Your nome app>.scm.azurewebsites.net/**.

Elementi forniti dalla console Kudu:

-	impostazioni di ambiente per l'applicazione
-	log in streaming
-	dump di diagnostica
-	console di debug in cui è possibile eseguire cmdlet di PowerShell e comandi DOS di base.


Inoltre, nel caso in cui l'applicazione generi eccezioni first-chance, è possibile usare Kudu e l'utilità della riga di comando Procdump dello strumento SysInternals per creare dump della memoria. I dump della memoria sono snapshot del processo e semplificano la risoluzione di problemi più complessi riscontrati nell'app Web.

Per altre informazioni sulle funzionalità disponibili in Kudu, vedere il post di blog relativo agli [strumenti online di Siti Web di Azure che è opportuno conoscere](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### 3\. Attenuare il problema

####	Ridimensionare l'app Web

Nel servizio app di Azure, per ottimizzare le prestazioni e la velocità effettiva è possibile modificare il piano in cui è in esecuzione l'applicazione. Ridimensionare un'app Web di Azure implica due azioni correlate: passare a un piano tariffario superiore e configurare determinate impostazioni una volta adottato il nuovo piano.

Per altre informazioni sul ridimensionamento, vedere [Ridimensionare un'app Web nel servizio app di Azure](web-sites-scale.md).

È anche possibile scegliere di eseguire l'applicazione in più di un'istanza. In questo modo, non solo si ottiene una maggiore capacità di elaborazione, ma si può usufruire della tolleranza di errore. Se il processo si arresta in un'istanza, l'altra istanza continuerà a gestire le richieste.

È possibile impostare il ridimensionamento manuale o automatico.

####	Usare la funzionalità AutoHeal

La funzionalità AutoHeal consente di riciclare il processo di lavoro per l'app in base alle impostazioni specificate, ad esempio modifiche di configurazione, richieste, limiti basati sulla memoria o il tempo necessario per l'esecuzione di una richiesta. Nella maggior parte dei casi, riciclare il processo costituisce il modo più veloce per risolvere un problema. Anche se è possibile riavviare l'app Web direttamente dall'interno del portale di Azure, la funzionalità AutoHeal eseguirà questa operazione automaticamente. È sufficiente aggiungere alcuni trigger nel file web.config radice per l'app Web. Si noti che queste impostazioni funzionano allo stesso modo anche per le applicazioni non .NET.

Per altre informazioni, vedere il post di blog relativo alla [correzione automatica di Siti Web di Azure](/blog/auto-healing-windows-azure-web-sites/).


####	Riavviare l'app Web

Questo è spesso il modo più semplice per risolvere problemi occasionali. Nel pannello dell'app Web del [portale di Azure](https://portal.azure.com/) sono disponibili le opzioni per arrestare o riavviare l'app.

 ![](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

È anche possibile gestire l'app Web usando Azure PowerShell. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).

<!---HONumber=AcomDC_0211_2016-->