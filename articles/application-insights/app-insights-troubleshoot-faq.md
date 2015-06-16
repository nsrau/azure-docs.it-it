<properties 
	pageTitle="Risoluzione dei problemi e domande e risposte su Application Insights" 
	description="Questo articolo contiene le risposte ai dubbi più comuni." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>
 
# Risoluzione dei problemi e domande e risposte - Application Insights in Anteprima di Microsoft Azure

+ [Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio](#q01)
+ [Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.](#q02)
+ [Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.](#q03)
+ [In Dati di analisi utilizzo non ci sono dati](#q04)
+ [Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?](#q05)
+ [In che modo è possibile modificare la risorsa di Azure in cui vengono visualizzati i dati?](#update)
+ [Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"](#emptykey)
+ [La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?](#q06)
+ [Quando si aggiunge Application Insights all'applicazione e si apre il portale Application Insights, è tutto completamente diverso rispetto alle catture di schermata.](#q07)
+ [Si può usare Application Insights per monitorare un server Web Intranet?](#q08)
+ [Come si ottengono dati per Windows Phone o Windows Store?](#q09)
+ [Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?](#q10)
+ [Perché ci sono due versioni di Application Insights?](#q11)
+ [In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?](#q13)
+ [Quali modifiche apporta Application Insights al progetto?](#q14)
+ [Come si fa a trovare i risultati in Application Insights?](#q15)
+ [Quali porte è necessario aprire nel firewall?](#q16)
+ [In Application Insights sono state abilitate tutte le funzionalità?](#q17)
+ [Altre informazioni](#next)



## <a name="q01"></a>Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio

+ Assicurarsi di disporre di [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827). È preinstallato con Strumenti Application Insights, che dovrebbe essere presente in Gestione estensioni.
+ Application Insights in Anteprima di Microsoft Azure attualmente è disponibile solo per progetti Web ASP.NET in C# o Visual Basic.
+ Se si dispone di un progetto esistente, passare a Esplora soluzioni e fare clic sul progetto Web (non su un altro progetto o sulla soluzione).. Dovrebbe essere presente una voce di menu "Aggiungi telemetria di Application Insights al progetto".
+ Se si crea un nuovo progetto, in Visual Studio scegliere File > Nuovo progetto e selezionare {Visual C#|Visual Basic} > Web > Applicazione Web ASP.NET.. Dovrebbe essere presente l'opzione Aggiungi Application Insights al progetto.

## <a name="q02"></a>Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.

Può succedere se la comunicazione con il portale Application Insights non è riuscita o in caso di problemi con l'account.

+ Verificare di avere specificato le credenziali di accesso per l'account Azure appropriato. Le credenziali di Microsoft Azure, visualizzate nella finestra di dialogo Nuovo progetto, possono essere diverse da quelle di Visual Studio Online visualizzate nella parte superiore destra di Visual Studio.
+ Attendere qualche istante, poi [aggiungere Application Insights al progetto esistente][start]..
+ Passare alle impostazioni dell'account Microsoft Azure e verificare la presenza di eventuali limitazioni. Provare ad aggiungere manualmente un'applicazione di Application Insights.


## <a name="q03"></a>Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.

+ È necessario chiudere e riaprire un pannello nel quale si attende la comparsa di dati. Nella versione corrente il contenuto di un pannello non viene aggiornato automaticamente.
+ Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
+ Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="q04"></a>In Dati di analisi utilizzo non ci sono dati

+ I dati provengono da script nelle pagine Web. Se Application Insights è stato aggiunto a un progetto Web esistente, [è necessario aggiungere manualmente gli script][start]..


## <a name="q05"></a>Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?

È possibile procedere in due modi:

* Scegliere Sfoglia, Application Insights, quindi il nome del progetto. Se non è presente alcun progetto, è necessario [aggiungere Application Insights al progetto Web in Visual Studio][start].

* In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto Web e scegliere Apri portale Application Insights.

## <a name="update"></a>In che modo è possibile modificare la risorsa di Azure alla quale il progetto invia i dati?

In Esplora soluzioni fare clic con il pulsante destro del mouse su`ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

## <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"

Sembra che si sia verificato un problema durante l'installazione di Application Insights o forse di un adattatore di registrazione.

In Esplora soluzioni fare clic con il pulsante destro del mouse su`ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa Application Insights o a riusarne una esistente.

## <a name="q06"></a>La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?

No. Mostra lo stato del servizio di Azure. Per vedere i risultati del test Web, scegliere Sfoglia > Application Insights > (applicazione) e quindi osservare i risultati del test Web. 


## <a name="q07"></a>Quando si aggiunge Application Insights all'applicazione e si apre il portale Application Insights, è tutto completamente diverso rispetto alle catture di schermata.

È possibile che sia in uso una [versione precedente di Strumenti Application Insights](http://msdn.microsoft.com/library/dn793604.aspx), che si connette alla versione Visual Studio Online.

Queste pagine della guida si riferiscono ad [Application Insights per Anteprima di Microsoft Azure][start], in dotazione con Visual Studio Update 3. 

## <a name="q08"></a>Si può usare Application Insights per monitorare un server Web Intranet?

Sì, è possibile monitorare l'integrità e l'utilizzo se il server è in grado di inviare dati alla rete Internet pubblica.

Se però si vuole eseguire test Web per il servizio, è necessario che sia accessibile dalla rete Internet pubblica.

## <a name="q09"></a>Come si ottengono dati per Windows Phone o Windows Store?

Questa funzionalità non è ancora supportata nella versione Microsoft Azure. Usare la [versione Visual Studio Online][older].


## <a name="q10"></a>Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?

Questa funzionalità non è ancora supportata nella versione Microsoft Azure. Sarà disponibile a breve. Per il momento è possibile provare la [versione precedente][older].


## <a name="q11"></a>Perché ci sono due versioni di Application Insights?

Il portale precedente fa parte di Visual Studio Online. Non saranno apportate altre modifiche sostanziali a quella versione. Se si usa una versione precedente degli strumenti Application Insights per Visual Studio, questi si connettono alla versione Visual Studio Online.

Visual Studio Update 3 include una versione preinstallata dei nuovi strumenti Application Insights. Questi si connettono al nuovo portale Application Insights, che è un componente di Anteprima di Microsoft Azure. È in corso il trasferimento di Application Insights in questo nuovo ambiente. Tale operazione non è ancora stato completata.

## <a name="q13"></a>In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?

1. Passare a Gestione estensioni di Visual Studio. 
2. Disinstallare Strumenti Application Insights.
3. Eseguire il [programma di installazione per la versione precedente degli strumenti](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) e leggere la relativa [guida introduttiva][older].

## <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?

Dipende dal tipo di progetto. Per un'applicazione Web:+


+ Aggiunge tre file al progetto:

 + ApplicationInsights.config. 
 + ai.js


+ Installa i pacchetti NuGet seguenti:

 -  *Application Insights API*, ovvero l'API principale

 -  *Application Insights API for Web Applications*, che consente di inviare i dati di telemetria dal server

 -  *Application Insights API for JavaScript Applications*, che consente di inviare i dati di telemetria dal client

    I pacchetti includono gli assembly seguenti:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Inserire elementi in:

 - Web.config

 - packages.config

+ (Solo per i nuovi progetti, se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente). Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito nella pagina master Views/Shared/_Layout.cshtml


## <a name="q15"></a>Come si fa a trovare i risultati in Application Insights?
1. Aprire Microsoft Azure:
 - In Visual Studio fare clic con il pulsante destro del mouse sul progetto di applicazione Web e scegliere **Apri portale di anteprima di Azure**.
 - In alternativa, in un Web browser è possibile aprire l'account in Anteprima di Microsoft Azure.

2. Scegliere Sfoglia, Application Insights, quindi selezionare il progetto.

## <a name="q16"></a>È presente un firewall tra il server o il computer di sviluppo e la rete Internet pubblica. Che traffico occorre consentire per abilitare Application Insights?

I dati di prestazioni e utilizzo vengono inviati alle porte TCP 80 e 443 verso dc.services.visualstudio.com and f5.services.visualstudio.com.

I test di disponibilità Web dipendono dall'accesso in ingresso al server Web sulla porta 80.

## <a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?

<table border="1">
<tr><th>Quali contenuti dovrebbero essere visualizzati</th><th>Come visualizzarli</th><th>Perché visualizzarli</th></tr>
<tr><td>Grafici di disponibilità</td><td><a href="../app-insights-monitor-web-app-availability/">Test Web</a></td><td>Sapere che l'app Web funziona</td></tr>
<tr><td>Prestazioni dell'app server: tempi di risposta, ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Aggiungere Application Insights al progetto</a><br/>o <br/><a href="../app-insights-monitor-performance-live-website-now/">installare Monitoraggio dello stato di Application Insights sul server</a></td><td>Rilevare i problemi di prestazioni</td></tr>
<!-- ####future#### <tr><td>Telemetria di dipendenza</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights sul server</a></td><td>Diagnosticare i problemi relativi a database o altri componenti esterni</td></tr> -->
<!-- #####74.1#### <tr><td>Dati globali server: CPU, memoria, ...</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights sul server</a></td><td>Diagnosticare i problemi di capacità</td></tr> --> 
<tr><td>Tracce dei log delle ricerche</td><td><a href="../app-insights-search-diagnostic-logs/">Aggiungere un adattatore di registrazione</a></td><td>Diagnosticare le eccezioni, i problemi di prestazioni</td></tr>
<tr><td>Nozioni di base dell'utilizzo del client: visualizzazioni pagina, utenti ricorrenti, ...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">inizializzatore JavaScript nelle pagine Web </a></td><td>Analisi dell'utilizzo</td></tr>
<tr><td>Metriche personalizzate del client</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Tenere traccia delle chiamate nelle pagine Web</a></td><td>Migliorare l'esperienza utente</td></tr>
<tr><td>Metriche personalizzate del server</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Tenere traccia delle chiamate nel codice del server</a></td><td>Business intelligence</td></tr>
</table>

Se il servizio Web è in esecuzione in una VM di Azure, è inoltre possibile [ottenere la diagnostica][azurediagnostic] in tale posizione.



[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 

<!--HONumber=46--> 
 