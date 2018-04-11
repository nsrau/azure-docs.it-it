---
title: Dettagli del piano tariffario Enterprise per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Dettagli del piano Enterprise

Application Insights ha due piani tariffari. Il piano predefinito è quello [Basic](app-insights-pricing.md), in cui sono incluse le stesse funzionalità del piano Enterprise senza costi aggiuntivi e la fatturazione viene effettuata principalmente in base al volume di dati inseriti. Se si usa Operations Management Suite, è preferibile scegliere il piano Enterprise, in cui sono previsti un addebito per ogni nodo e quantità di dati giornaliere e quindi la fatturazione viene effettuata in base ai dati inseriti oltre la quantità prevista.

Per i prezzi correnti nella valuta e nell'area locali, vedere la [pagina sui prezzi di Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

## <a name="heres-how-the-enterprise-plan-works"></a>Ecco come funziona il piano Enterprise

* Si paga per ogni nodo che invii dati di telemetria per le app nel piano Enterprise.
 * Un *nodo* è una macchina server fisica o virtuale oppure un'istanza del ruolo PaaS (piattaforma distribuita come servizio) che ospita l'app.
 * Computer di sviluppo, browser client e dispositivi mobili non sono conteggiati come nodi.
 * Se l'app dispone di diversi componenti che inviano dati di telemetria, ad esempio un servizio Web e un lavoro back-end, questi vengono conteggiati separatamente.
 * I dati di [Live Metrics Stream](app-insights-live-stream.md) non vengono conteggiati per la determinazione dei prezzi.* In una sottoscrizione, i costi vengono calcolati per ogni nodo, non per ogni app. Se si dispone di cinque nodi che inviano dati di telemetria per 12 app, l'addebito sarà per cinque nodi.
* Sebbene gli addebiti siano fatturati mensilmente, quelli effettivi hanno luogo solo nelle ore durante le quali un nodo invia dati di telemetria da un'app. La tariffa oraria è la tariffa mensile fatturata divisa per 744 (il numero di ore in un mese di 31 giorni).
* Viene fornita un'allocazione di volume di dati di 200 MB al giorno per ciascun nodo rilevato (con granularità oraria). L'allocazione di dati non usata non viene trasferita al giorno successivo.
 * Scegliendo l'opzione di prezzo Enterprise, ciascuna sottoscrizione ottiene una quantità di dati giornaliera inclusa in base al numero di nodi che inviano dati di telemetria alle risorse di Application Insights in quella sottoscrizione. Se si dispone quindi di 5 nodi che inviano dati tutto il giorno, sarà applicata una quantità inclusa di 1 GB a tutte le risorse di Application Insights in quella sottoscrizione. Non è importante se determinati nodi inviano più dati di altri, perché i dati inclusi vengono condivisi tra tutti i nodi. Se un determinato giorno le risorse di Application Insights ricevono più dati di quelli inclusi nell'allocazione giornaliera per la sottoscrizione, si applicheranno gli addebiti di dati in eccedenza per GB. 
 * La quantità di dati giornaliera viene calcolata come numero di ore del giorno (fuso orario UTC) in cui ogni nodo invia dati di telemetria, diviso per 24 e moltiplicato per 200 MB. Se si dispone di 4 nodi che inviano dati di telemetria per 15 ore su 24 durante un giorno, i dati inclusi per tale giorno saranno ((4 x 15) / 24) x 200 MB = 500 MB. Al prezzo di USD 2,30 per GB di eccedenza di dati, l'addebito sarà di USD 1,15 supponendo che i nodi quel giorno inviino 1 GB di dati.
 * La quantità giornaliera del piano Enterprise non viene condivisa con le applicazioni per cui è stata scelta l'opzione Basic. Inoltre, eventuali quantità non usate in un determinato giorno non vengono accumulate. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Di seguito sono riportati alcuni esempi di determinazione di un conteggio di nodi specifico

| Scenario                               | Conteggio giornaliero totale dei nodi |
|:---------------------------------------|:----------------:|
| 1 applicazione usa 3 istanze del Servizio app di Azure e 1 server virtuale | 4 |
| 3 applicazioni in esecuzione su 2 macchine virtuali. Le risorse di Application Insights per queste applicazioni si trovano nella stessa sottoscrizione e nello stesso piano Enterprise | 2 | 
| 4 applicazioni le cui risorse di Application Insights si trovano nella stessa sottoscrizione. Ogni applicazione esegue 2 istanze durante 16 ore di scarso traffico e 4 istanze durante 8 ore di traffico intenso. | 13.33 | 
| Servizi cloud con ruolo di lavoro 1 e 1 ruolo Web, ognuno dei quali esegue 2 istanze | 4 | 
| Cluster Service Fabric a 5 nodi che esegue 50 microservizi. Ciascun microservizio esegue 3 istanze | 5|

* Il comportamento preciso del conteggio dei nodi dipende dall'SDK di ApplicationInsight usato dall'applicazione. 
  * Nelle versioni SDK 2.2 e successive, sia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) che [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) di Application Insights possono riportare ciascun host applicazione come nodo, ad esempio il nome del computer per gli host server fisici e le macchine virtuali o il nome dell'istanza in caso di servizi cloud.  L'unica eccezione è data dalle applicazioni che usano solo [.NET Core](https://dotnet.github.io/) e Core SDK di Application Insights. In questo caso, verrà riportato un solo nodo per tutti gli host poiché il nome host non è disponibile. 
  * Per le versioni precedenti di SDK, [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) avrà lo stesso comportamento delle nuove versioni dell'SDK, tuttavia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) riporterà solamente un nodo a prescindere dal numero effettivo degli host applicazione. 
  * Se l'applicazione usa SDK per impostare roleInstance su un valore personalizzato, per impostazione predefinita verrà usato quello stesso valore per determinare il conteggio dei nodi. 
  * Se si usa una nuova versione di SDK con un'app eseguita da computer client o dispositivi mobili, è possibile che il conteggio dei nodi restituisca un numero molto elevato (per via del numero elevato di computer client o dispositivi mobili). 
