---
title: Impatto sull'utilizzo di Azure Application Insights | Microsoft Docs
description: "Analizzare in che modo diverse proprietà influiscono potenzialmente sui tassi di conversione di parti delle app."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/25/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: d76db02647ce878343f60fc84cf063c5b7833438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="impact-analysis-with-application-insights"></a>Analisi di impatto con Application Insights

L'impatto analizza in che modo i tempi di caricamento e altre proprietà influiscono sui tassi di conversione di varie parti dell'app. Più precisamente, l'impatto rileva come **una dimensione** di una **visualizzazione pagina**, un **evento personalizzato** o una **richiesta** influisce sull'utilizzo di una **visualizzazione pagina** o un **evento personalizzato** diverso. 

![Strumento Impatto](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Quale funzione svolge lo strumento Impatto?

Impatto può essere considerato lo strumento fondamentale per risolvere eventuali controversie con eventuali membri del team circa l'impatto che può avere la lentezza in alcune aree del sito sulla volontà degli utenti di soffermarsi o meno. Anche se gli utenti possono tollerare una certa lentezza, lo strumento Impatto offre informazioni dettagliate su come trovare il giusto equilibrio tra ottimizzazione e prestazioni allo scopo di massimizzare la conversione degli utenti.

L'analisi delle prestazioni, tuttavia, è solo una parte delle funzionalità dello strumento Impatto. Poiché lo strumento supporta dimensioni ed eventi personalizzati, sarà possibile con pochi clic rispondere a numerose domande, ad esempio qual è la correlazione tra la scelta di un browser utente e diversi tassi di conversione.

![Screenshot relativo alla conversione in base ai browser](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Per poter usare lo strumento Impatto, la risorsa di Application Insights deve contenere visualizzazioni pagina o eventi personalizzati. Vedere le [informazioni su come configurare l'app per la raccolta automatica delle visualizzazioni di pagina con Application Insights JavaScript SDK](app-insights-javascript.md). Inoltre, tenere presente che, dal momento che si stanno analizzando le correlazioni, le dimensioni del campione sono fondamentali.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Il tempo di caricamento di una pagina ha impatto sul numero di utenti che eseguono la conversione nella pagina?

Per iniziare a rispondere alle domande con lo strumento Impatto, scegliere una visualizzazione pagina, un evento personalizzato o una richiesta iniziale.

![Strumento Impatto](./media/app-insights-usage-impact/0002-dropdown.png)

1. Selezionare una visualizzazione pagina dall'elenco a discesa **For the page view** (Per la visualizzazione pagina).
2. Lasciare l'elenco a discesa **analyze how its** (analizzare come l'elemento) impostato sul valore predefinito **Durata**. In questo contesto **Durata** è un alias di **Tempo di caricamento pagina**.
3. Per l'elenco a discesa **impacts the usage of** (ha impatto sull'utilizzo di) selezionare un evento personalizzato. Questo evento deve corrispondere a un elemento dell'interfaccia utente nella visualizzazione pagina selezionata nel passaggio 1.

![Screenshot dei risultati](./media/app-insights-usage-impact/0003-results.png)

In questa istanza, con l'aumento del tempo di caricamento di **Product Page** il tasso di conversione in **Purchase Product clicked** diminuisce. In base alla distribuzione precedente, un tempo di caricamento pagina ottimale pari a 3,5 secondi può essere destinato a ottenere un tasso di conversione potenziale pari al 55%. Non esiste attualmente una correlazione tra ulteriori miglioramenti delle prestazioni per ridurre il tempo di caricamento al di sotto di 3,5 secondi e maggiori vantaggi in termini di conversione.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Cosa accade se le visualizzazioni pagina o i tempi di caricamento vengono monitorati in modi personalizzati?

Lo strumento Impatto supporta proprietà e misure sia standard che personalizzate. È possibile usare entrambe. Al posto della durata, usare i filtri sugli eventi primari e secondari per ottenere risultati più specifici.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Gli utenti di aree o paesi diversi presentano tassi di conversione diversi?

1. Selezionare una visualizzazione pagina dall'elenco a discesa **For the page view** (Per la visualizzazione pagina).
2. Scegliere "Country or region" (Paese o area) nell'elenco a discesa **analyze how its** (analizzare come l'elemento).
3. Nell'elenco a discesa **impacts the usage of** (ha impatto sull'utilizzo di) selezionare un evento personalizzato corrispondente a un elemento dell'interfaccia utente nella visualizzazione pagina scelta nel passaggio 1.

In questo caso i risultati non rientrano più in un modello di asse x continuo come nel primo esempio. Al contrario, viene presentata una visualizzazione simile a un grafico a imbuto segmentato. Ordinare in base all'opzione **Utilizzo** per visualizzare la variazione di conversione dell'evento personalizzato in base al paese.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Come vengono calcolati questi tassi di conversione dallo strumento Impatto?

A un livello sottostante, lo strumento Impatto si basa sul [coefficiente di correlazione di Pearson] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). I risultati calcolati sono compresi tra -1 e 1, con -1 che rappresenta zero correlazione e 1 che rappresenta una correlazione positiva.

L'analisi di impatto funziona in base alla suddivisione di base seguente:

_A_ = visualizzazione pagina/evento personalizzato/richiesta principale selezionato nel primo elenco a discesa, **For the page view** (Per la visualizzazione pagina).

_B_ = visualizzazione pagina/evento personalizzato secondario selezionato in **impacts the usage of** (ha impatto sull'utilizzo di).

Lo strumento Impatto esamina un campione di tutte le sessioni di utenti nell'intervallo di tempo selezionato. Per ogni sessione esegue la ricerca di tutte le occorrenze di _A_.

Le sessioni vengono quindi suddivise in due diversi tipi di _sessioni secondarie_ in base a una delle due condizioni seguenti:

- Una sessione secondaria convertita è costituita da una sessione che termina con un evento _B_ e comprende tutti gli eventi _A_ che si verificano prima di _B_.
- Una sessione secondaria non convertita si verifica quando tutti gli eventi _A_ non terminano con un evento _B_.

Il calcolo finale dell'impatto varia a seconda che l'analisi venga eseguita in base alla metrica o alla dimensione. Per le metriche viene calcolata una media di tutti gli eventi _A_ in una sessione secondaria, mentre per le dimensioni il valore di ogni evento _A_ contribuisce _1/N_ al valore assegnato all'evento _B_. _N_ è il numero di eventi _A_ nella sessione secondaria.

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    - [Grafici a imbuto](usage-funnels.md)
    - [Conservazione](app-insights-usage-retention.md)
    - [Flussi degli utenti](app-insights-usage-flows.md)
    - [Cartelle di lavoro](app-insights-usage-workbooks.md)
    - [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)