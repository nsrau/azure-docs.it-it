---
title: Modello di dati di Azure Application Insights Telemetry - Telemetria delle richieste | Microsoft Docs
description: Modello di dati di Application Insights per la telemetria delle richieste
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 0073f38097ffbebd669754eac5f2d48a620941bf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria delle richieste: modello di dati di Application Insights

In [Application Insights](app-insights-overview.md), un elemento di telemetria delle richieste rappresenta la sequenza logica di esecuzione attivata da una richiesta esterna all'applicazione. Ogni esecuzione di richiesta è identificato da un `ID` univoco e un `url` contenente tutti i parametri di esecuzione. È possibile raggruppare le richieste in base al `name` logico e definire il `source` della richiesta. L'esecuzione del codice può restituire un campo `success` o `fail` e ha un campo `duration` specificato. Le esecuzioni con esito positivo e negativo possono essere ulteriormente raggruppate in base a `resultCode`. L'ora di inizio della telemetria delle richieste è definita a livello di busta.

La telemetria delle richieste supporta il modello di estendibilità standard usando `properties` e `measurements` personalizzate.

## <a name="name"></a>NOME

Il nome della richiesta rappresenta il percorso di codice intrapreso per elaborare la richiesta. Un valore di cardinalità basso consente di raggruppare meglio le richieste. Per le richieste HTTP, rappresenta il metodo HTTP e un modello di percorso URL, ad esempio `GET /values/{id}`, senza il valore di `id` effettivo.

Web Application Insights SDK invia il nome della richiesta "così com'è" per quanto riguarda le maiuscole e minuscole. Il raggruppamento nell'interfaccia utente considera la distinzione tra maiuscole e minuscole, pertanto `GET /Home/Index` viene considerato diverso da `GET /home/INDEX`, anche se spesso riguardano l'esecuzione della stessa azione e dello stesso controller. Ciò accade perché gli URL rilevano in genere [la distinzione tra maiuscole e minuscole](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Si consiglia di verificare se tutti gli errori `404` si sono verificati per gli URL digitati in maiuscolo. Altre informazioni sulla raccolta di nomi di richiesta da ASP.Net Web SDK sono disponibili nel [post di blog](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Lunghezza massima: 1024 caratteri

## <a name="id"></a>ID

Identificatore dell'istanza di una chiamata di richiesta. Usato per la correlazione tra richiesta e altri elementi di telemetria. L'ID deve essere globalmente univoco. Per altre informazioni vedere la pagina relativa alla [correlazione](application-insights-correlation.md).

Lunghezza massima: 128 caratteri

## <a name="url"></a>Url

URL della richiesta con tutti i parametri di stringa di query.

Lunghezza massima: 2048 caratteri

## <a name="source"></a>Sorgente

Origine della richiesta. Esempi sono la chiave di strumentazione o l'indirizzo IP del chiamante. Per altre informazioni vedere la pagina relativa alla [correlazione](application-insights-correlation.md).

Lunghezza massima: 1024 caratteri

## <a name="duration"></a>Duration

Durata della richiesta in formato: `DD.HH:MM:SS.MMMMMM`. Deve essere un valore positivo e inferiore a `1000` giorni. Questo campo è obbligatorio in quanto la telemetria delle richieste rappresenta l'operazione con un inizio e una fine.

## <a name="response-code"></a>Codice della risposta

Risultato dell'esecuzione di una richiesta. Codice di stato HTTP per le richieste HTTP. Potrebbe essere un valore `HRESULT` o un tipo di eccezione per altri tipi di richiesta.

Lunghezza massima: 1024 caratteri

## <a name="success"></a>Success

Indicazione di chiamata con esito positivo o con esito negativo. Questo campo è obbligatorio. Se non è impostata in modo esplicito su `false` la richiesta viene considerata con esito positivo. Impostare questo valore su `false` se l'operazione è stata interrotta da un'eccezione o un codice di errore restituito.

Per le applicazioni Web, Application Insights definisce una richiesta come non riuscita quando il codice di risposta è minore di `400` o uguale a `401`. Esistono tuttavia casi in cui questo mapping predefinito non corrisponde alla semantica dell'applicazione. Il codice di risposta `404` può indicare "Nessun record" e quindi fare parte del normale flusso. Può indicare anche un collegamento interrotto. Nel caso di collegamenti interrotti, è anche possibile implementare una logica più avanzata. È possibile contrassegnare i collegamenti interrotti come errori solo se tali collegamenti vengono individuati nello stesso sito analizzando il riferimento dell'URL. È possibile contrassegnarli come errori anche quando l'accesso viene effettuato dall'applicazione per dispositivi mobili dell'azienda. I codici `301` e `302` indicano un errore quando si accede dal client che non supporta il reindirizzamento.

Il codice `206` di contenuto parzialmente accettato può indicare un errore di una richiesta globale. L'endpoint di Application Insights riceve, ad esempio, un batch di elementi di telemetria come una singola richiesta. Restituisce `206` quando alcuni elementi del batch non sono stati elaborati correttamente. Un aumento della frequenza di `206` indica un problema che richiede attenzione. Si applica una logica simile a `207` - Multi-Status in cui l'esito positivo può essere il peggiore dei codici di risposta separati.

Altre informazioni sui codici di stato e dei risultati della richiesta sono disponibili nel [post del blog](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Misure personalizzate

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere dati di telemetria della richiesta personalizzata](app-insights-api-custom-events-metrics.md#trackrequest)
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- Informazioni su come [configurare un'applicazione ASP.NET Core](app-insights-asp-net.md) con Application Insights.
- Verificare quali [piattaforme](app-insights-platforms.md) supportano Application Insights.
