<properties 
	pageTitle="Servizio app di Azure e impatto sui servizi di Azure esistenti" 
	description="Viene illustrato come il nuovo servizio app di Azure e le relative funzionalità influiscono sui servizi esistenti in Azure." 
	authors="yochayk" 
	writer="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Servizio app di Azure e i servizi di Azure esistenti

Questo articolo illustra le modifiche ai servizi di Azure esistenti come parte delle modifiche introdotte al fine di raggruppare vari servizi di Azure nel [servizio app di Azure](http://azure.microsoft.com/services/app-service/), una nuova offerta integrata.

## Informazioni generali 

Il [servizio app di Azure](http://azure.microsoft.com/services/app-service/) è un servizio cloud nuovo ed esclusivo che consente agli sviluppatori di creare app mobili e Web per qualsiasi piattaforma e dispositivo. Il servizio app viene offerto come soluzione integrata progettata per snellire le ripetitive procedure di scrittura del codice, integrarsi con i sistemi aziendali e SaaS e automatizzare i processi aziendali, adattandosi al contempo alle esigenze di sicurezza, affidabilità e scalabilità.

Il servizio app riunisce i servizi di Azure esistenti [Siti Web](http://azure.microsoft.com/services/websites/), [Servizi mobili](http://azure.microsoft.com/services/mobile-services/) e [Servizi Biztalk](http://azure.microsoft.com/services/biztalk-services/) in un unico servizio combinato, a cui sono state aggiunte nuove funzionalità avanzate.  Il servizio app consente di ospitare i seguenti tipi di app: 

-   App Web
-   App mobili
-   App API
-   App logiche

La tabella seguente illustra la corrispondenza tra i servizi di Azure esistenti e il nuovo servizio app, nonché i tipi di app disponibili al suo interno.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Servizio di Azure esistente</th>
<th align="left", style="width:10%">Servizio app di Azure</th>
<th align="left", style="width:80%">Cosa è cambiato</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Siti Web di Azure</td>
<td align="left">App Web</td>
<td align="left"><li>Per quanto riguarda Siti Web di Azure, le modifiche si riducono esclusivamente al cambio di nome  da siti Web ad app Web.
<p><li>Tutte le istanze esistenti di Siti Web sono ora app Web nel servizio app.</p>
<p><li>È possibile accedere ai siti web esistenti tramite il <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portale di Azure</a>, in cui tutti i siti Web esistenti saranno visualizzati sotto <em>App Web</em>.</p>
<p><li><em>Piano di hosting Web</em> è ora <em>Piano di servizio app</em>. Un <em>Piano di servizio app</em> può ospitare qualsiasi tipo di servizio app, ad esempio app Web, mobili, logiche o API.</p>
<p><li>App Web del servizio app di Azure si trova ora in stato di disponibilità generale.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Altre informazioni sul servizio app Web.</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Servizi mobili di Azure</td>
<td align="left">App mobili</td>
<td align="left"><p><li>Servizi mobili continua ad essere disponibile come servizio autonomo e rimane completamente supportato.</p>
<p><li>App mobili è un nuovo tipo di app nel servizio app che integra tutte le funzionalità di Servizi mobili e ne aggiunge di ulteriori. App mobili è disponibile nella versione di anteprima pubblica.</p>
<p><li>È molto facile eseguire la migrazione da <a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">Servizi mobili ad App mobili</a>. Poiché App mobili si trova ancora in versione di anteprima, non è consigliabile usare il servizio per l'esecuzione di app in ambiente di produzione.</p>
<p><li>Come parte del servizio app, App mobili offre nuove funzionalità oltre a quelle di Servizi mobili, come  l'integrazione con sistemi locali e SaaS, slot di gestione temporanea, Processi Web, opzioni di scalabilità migliorate e altro ancora.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Altre informazioni su App mobili</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">App API</td>
<td align="left">
<p><li>App API è un nuovo tipo di app nel servizio app, che consente di creare e usare API nel cloud con estrema facilità.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Altre informazioni su App API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">App logiche</td>
<td align="left">
<p><li>App logiche è un nuovo tipo di app nel servizio app, che consente di automatizzare i processi aziendali con estrema facilità.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Altre informazioni su App logiche</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Servizi BizTalk di Azure</td>
<td align="left">App API BizTalk</td>
<td align="left">
<li><p>Servizi BizTalk continua a essere disponibile come servizio autonomo e rimane completamente supportato.</p>
<li><p>Tutte le funzionalità di Servizi BizTalk sono integrate nel servizio app come app API, consentendo agli utenti di eseguire l'integrazione di applicazioni aziendali e di scenari B2B con qualsiasi tipo di app dei servizi app.</p>
<li><p>Con App logiche è ora possibile automatizzare i processi aziendali usando un'esperienza di progettazione visiva per la creazione di flussi di lavoro.</p></td>
</tr>
</tbody>
</table>

Per altre informazioni, vedere la [documentazione relativa al servizio app](http://azure.microsoft.com/documentation/services/app-service/).


<!--HONumber=49-->