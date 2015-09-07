<properties 
	pageTitle="Risoluzione dei problemi e domande su Application Insights"
	description="Questo articolo contiene le risposte ai dubbi più comuni su Application Insights di Visual Studio ai dubbi più comuni."
	services="application-insights"
	documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags 
	ms.service="application-insights"
	ms.workload="mobile"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="awills"/>
 
# Risoluzione dei problemi e domande - Application Insights per ASP.NET

## Si può usare Application Insights con ...?

[Vedere le piattaforme][platforms].

## È gratuito?

* Sì, se si sceglie il [piano tariffario](app-insights-pricing.md) gratuito. In tal modo, è possibile ottenere la maggior parte delle funzionalità e una quota dati soddisfacente. 
* Per registrarsi in Microsoft Azure è necessario fornire i dati della propria carta di credito, ma verranno addebitati costi solo se si usa un altro servizio di Azure a pagamento o si effettua esplicitamente l'aggiornamento a un livello non gratuito.
* Se la propria app invia una quantità di dati superiore a quella consentita dalla quota mensile, la registrazione si interrompe. In tal caso, è possibile scegliere di pagare o attendere che la quota venga reimpostata alla fine del mese.
* I dati relativi alla sessione e all'utilizzo di base non sono soggetti a una quota.
* È anche previsto un periodo di valutazione gratuita di 30 giorni durante il quale è possibile usufruire delle funzionalità Premium senza alcun addebito.
* A ogni risorsa dell'applicazione è associata una quota separata ed è possibile impostare il relativo piano tariffario in modo indipendente rispetto alle altre.

#### Quali vantaggi si ottengono a pagamento?

* [Quota dati mensile](http://azure.microsoft.com/pricing/details/application-insights/) più elevata.
* Possibilità di pagare l'"eccedenza" per continuare a raccogliere dati se viene superata la quota mensile. Quando i dati superano la quota prevista, l'addebito dei costi avviene per MB.
* [Esportazione continua](app-insights-export-telemetry.md).

## Aggiunta dell'SDK

#### <a name="q01"></a>Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio

+ Assicurarsi di disporre di [Visual Studio 2013 Update 3 o versioni successive](http://go.microsoft.com/fwlink/?LinkId=397827). È fornito con gli Strumenti Application Insights preinstallati.
+ Nonostante gli strumenti non supportino tutti i tipi di applicazioni, è ancora probabilmente possibile aggiungere manualmente un Application Insights SDK al progetto. Usare [questa procedura][windows]. 


#### <a name="q02"></a>Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.

Ciò può verificarsi se:

* la comunicazione con il portale Application Insights si interrompe;
* se si verificano problemi con l'account;
* se si ha [accesso in sola lettura alla sottoscrizione o al gruppo in cui è stato effettuato il tentativo di creare la nuova risorsa](app-insights-resources-roles-access-control.md).

Soluzione:

+ Verificare di avere specificato le credenziali di accesso per l'account Azure appropriato. In alcune versioni precedenti degli strumenti, le credenziali di Microsoft Azure, visualizzate nella finestra di dialogo Nuovo progetto, possono essere diverse da quelle di Visual Studio Online visualizzate nella parte superiore destra di Visual Studio.
+ Nel browser, verificare di avere accesso al [portale di Azure](https://portal.azure.com). Aprire le impostazioni e vedere se sono presenti restrizioni.
+ [Aggiungere Application Insights a un nuovo progetto][start]\: in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere "Aggiungi Application Insights".
+ Se il problema continua a verificarsi, seguire la [procedura manuale](app-insights-start-monitoring-app-health-usage.md) per aggiungere una risorsa al portale e quindi aggiungere l'SDK al progetto. 

#### <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"

Sembra che si sia verificato un problema durante l'installazione di Application Insights o forse di un adattatore di registrazione.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa di Application Insights o a riusarne una esistente.


#### <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?

Dipende dal tipo di progetto. Per un'applicazione Web:


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

+ (Solo per i nuovi progetti, se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente.) Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito nella pagina master Views/Shared/\_Layout.cshtml

####<a name="NuGetBuild"></a>Un messaggio sul server di compilazione informa che i pacchetti NuGet sono mancanti, nonostante tutto proceda correttamente sui computer di sviluppo

Vedere gli articoli relativi al [ripristino dei pacchetti NuGet](http://docs.nuget.org/Consume/Package-Restore) e al [ripristino automatico dei pacchetti](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a>Un messaggio informa che i riferimenti del progetto ai pacchetti NuGet mancano sul computer quando si prova a compilare dopo l'aggiornamento alla versione 0.17 o successiva dei pacchetti NuGet.

Se viene visualizzato questo errore dopo aver eseguito l'aggiornamento alla versione 0.17 o successiva dei pacchetti NuGet, è necessario modificare il file del progetto e rimuovere le destinazioni BCL residue.

A tale scopo, effettuare l'operazione seguente:

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Scarica progetto.
2. Fare di nuovo clic con il pulsante destro del mouse sul progetto e scegliere Modifica *nomeprogetto.csproj* 
3. Passare alla fine del file di progetto e rimuovere le destinazioni BCL simili a: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Salvare il file.
5. Fare clic con il pulsante destro del mouse sul progetto e scegliere Ricarica *nomeprogetto.csproj*

## In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?

Vedere le [note sulla versione](app-insights-release-notes.md) dell'SDK appropriato per il tipo di applicazione.


## Dati non presenti

#### <a name="q03"></a>Dopo aver aggiunto Application Insights correttamente ed avere eseguito l'app, nel portale non vengono visualizzati dati.

+ Nella pagina Panoramica, fare clic sul riquadro Ricerca per aprire Ricerca diagnostica. I dati verranno visualizzati prima qui.
+ Fare clic sul pulsante Aggiorna. Il pannello viene automaticamente aggiornato periodicamente, ma è anche possibile farlo manualmente. L'intervallo di aggiornamento è più lungo per intervalli di tempo maggiori.
+ Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
+ Controllare anche il [blog sullo stato](http://blogs.msdn.com/b/applicationinsights-status/).
+ Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.
+ Se è necessario usare un proxy per inviare all'esterno della rete aziendale, impostare [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in Web.config
+ Windows Server 2008: assicurarsi che siano stati installati i seguenti aggiornamenti: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a>In Dati di analisi utilizzo non ci sono dati per il mio sito Web

+ I dati provengono da script nelle pagine Web. Se Application Insights è stato aggiunto a un progetto Web esistente, [è necessario aggiungere manualmente gli script][start].
+ Verificare che il sito non venga visualizzato in modalità di compatibilità in Internet Explorer.
+ Usare la funzionalità di debug del browser (F12 in alcuni browser, quindi scegliere Rete) per verificare che i dati vengano inviati a dc.services.visualstudio.com.

#### <a name="q08"></a>Si può usare Application Insights per monitorare un server Web Intranet?

Sì, è possibile monitorare l'integrità e l'utilizzo se il server è in grado di inviare dati alla rete Internet pubblica.

Se però si vuole eseguire test Web per il servizio, è necessario che sia accessibile dalla rete Internet pubblica sulla porta 80.

#### Si può monitorare un server Web intranet che non ha accesso a Internet pubblico?

È necessario predisporre un proxy che possa inoltrare le chiamate POST https a dc.services.visualstudio.com

#### Non vengono più visualizzati i dati disponibili in precedenza

* Controllare il [blog sullo stato](http://blogs.msdn.com/b/applicationinsights-status/).
* È stata raggiunta la quota mensile relativa ai punti dati? Per saperlo, aprire Impostazioni/Quota e Prezzi. Se la quota è stata raggiunta, è possibile aggiornare il piano oppure pagare per disporre di ulteriore capacità. Vedere lo [schema dei prezzi](http://azure.microsoft.com/pricing/details/application-insights/).



## Il portale

#### <a name="q05"></a>Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?

È possibile procedere in due modi:

* Scegliere Sfoglia, Application Insights, quindi il nome del progetto. Se non è presente alcun progetto, è necessario [aggiungere Application Insights al progetto Web in Visual Studio][start].

* In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto Web e scegliere Apri portale Application Insights.


#### <a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.


#### <a name="q06"></a>La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?

No. Mostra lo stato del servizio di Azure. Per vedere i risultati del test Web, scegliere Sfoglia > Application Insights > (applicazione) e quindi osservare i risultati del test Web.


#### <a name="q07"></a>Quando si aggiunge Application Insights all'applicazione e si apre il portale Application Insights, è tutto completamente diverso rispetto alle catture di schermata.

Potrebbe essere in uso la [versione precedente di Application Insights SDK](http://msdn.microsoft.com/library/dn793604.aspx), che si connette alla versione Visual Studio Online.

Queste pagine della guida si riferiscono ad [Application Insights per Anteprima di Microsoft Azure][start], in dotazione con Visual Studio 2013 Update 3 e versioni successive.

#### <a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?

Dare un'occhiata all'argomento relativo a [conservazione dei dati e privacy][data].

## Registrazione

#### <a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?

I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

## Sicurezza

#### I dati personali sono al sicuro nel portale? Per quanto tempo vengono conservati?

Vedere l'argomento relativo a [conservazione dei dati e privacy][data].


## <a name="q17"></a>In Application Insights sono state abilitate tutte le funzionalità?

<table border="1">
<tr><th>Elementi che dovrebbero essere visualizzati</th><th>Come ottenerli</th><th>Perché si vuole ottenerli</th></tr>
<tr><td>Grafici di disponibilità</td><td><a href="../app-insights-monitor-web-app-availability/">Test Web</a></td><td>Stabilire se l'app Web è attiva</td></tr>
<tr><td>Prestazioni dell'app server: tempi di risposta, ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Aggiungere Application Insights al progetto</a><br/>oppure <br/><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights nel server</a> (o scrivere il proprio codice per <a href="../app-insights-api-custom-events-metrics/#track-dependency">il tracciamento delle dipendenze</a>)</td><td>Rilevare i problemi di prestazioni</td></tr>
<tr><td>Telemetria di dipendenza</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installare Monitoraggio dello stato di Application Insights nel server</a></td><td>Diagnosticare i problemi relativi a database o altri componenti esterni</td></tr>
<tr><td>Ricavare analisi dello stack dalle eccezioni</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserire chiamate TrackException nel codice</a> (ma alcune sono segnalate automaticamente)</td><td>Rilevare e diagnosticare le eccezioni</td></tr>
<tr><td>Eseguire la ricerca di tracce dei log</td><td><a href="../app-insights-search-diagnostic-logs/">Aggiungere un adattatore di registrazione</a></td><td>Diagnosticare le eccezioni, problemi di prestazioni</td></tr>
<tr><td>Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ...</td><td><a href="../app-insights-javascript/">Inizializzatore JavaScript nelle pagine Web</a></td><td>Analisi dell'utilizzo</td></tr>
<tr><td>Metriche personalizzate client</td><td><a href="../app-insights-api-custom-events-metrics/">Tenere traccia delle chiamate nelle pagine Web</a></td><td>Migliorare l'esperienza utente</td></tr>
<tr><td>Metriche personalizzate server</td><td><a href="../app-insights-api-custom-events-metrics/">Tenere traccia delle chiamate nel codice del server</a></td><td>Business intelligence</td></tr>
</table>

Se il servizio Web è in esecuzione in una VM di Azure, è anche possibile [ottenere la diagnostica][azurediagnostic] in tale posizione.

## Automazione

È possibile [scrivere uno script di PowerShell](app-insights-powershell-script-create-resource.md) per creare una risorsa di Application Insights.


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=August15_HO9-->