---
title: Analizzare e condividere i dati di utilizzo con cartelle di lavoro interattive in Azure Application Insights | Microsoft docs
description: Analisi demografica degli utenti dell'app Web.
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3edaefa942b834a070c55fe28daf60c74ea9f59d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Analizzare e condividere i dati di utilizzo con cartelle di lavoro interattive in Application Insights

Le cartelle di lavoro combinano le visualizzazioni dei dati di [Azure Application Insights](app-insights-overview.md), le [query di Analisi](app-insights-analytics.md) e il testo in documenti interattivi. Le cartelle di lavoro possono essere modificate da altri membri del team con accesso alla stessa risorsa di Azure. Ciò significa che le query e i controlli usati per creare una cartella di lavoro sono disponibili ad altri utenti che leggono la cartella di lavoro semplificandone l'esplorazione, l'estensione e la ricerca di errori.

Le cartelle di lavoro sono utili per scenari simili ai seguenti:

* Esplorazione dell'utilizzo dell'app quando non si conoscono le metriche di interesse in anticipo, ovvero il numero di utenti, i tassi di fidelizzazione, i tassi di conversione e così via. A differenza di altri strumenti di analisi dell'utilizzo di Application Insights, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi risultando ideali per questo tipo di esplorazione in formato libero.
* Descrizione al proprio team delle prestazioni di una nuova funzionalità rilasciata visualizzando i conteggi degli utenti per le interazioni chiave e altre metriche.
* Condivisione dei risultati di un esperimento A/B nell'app con altri membri del team. È possibile descrivere gli obiettivi dell'esperimento usando testo e visualizzare ogni metrica di utilizzo e query di Analisi usate per valutare l'esperimento con callout chiari che indicano se la singola metrica è al di sopra o al di sotto del target.
* Segnalazione dell'impatto di un'interruzione nell'utilizzo dell'app, combinazione dei dati, spiegazione del testo e descrizione dei passaggi successivi per impedire interruzioni future.

> [!NOTE]
> La risorsa di Application Insights deve contenere visualizzazioni di pagina o eventi personalizzati per usare le cartelle di lavoro. Vedere le [informazioni su come configurare l'app per la raccolta automatica delle visualizzazioni di pagina con Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Modifica, ridisposizione, clonazione ed eliminazione di sezioni delle cartelle di lavoro

Una cartella di lavoro è costituita da sezioni: visualizzazioni dell'utilizzo modificabili separatamente, grafici, tabelle, testo o risultati delle query di Analisi.

Per modificare il contenuto di una sezione di una cartella di lavoro, fare clic sul pulsante **Modifica** sotto e a destra della sezione.

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/app-insights-usage-workbooks/editing-controls.png)

1. Dopo aver modificato una sezione, fare clic su **Modifica completata** nell'angolo inferiore sinistro della sezione.

2. Per creare un duplicato di una sezione, fare clic sull'icona **Clona questa sezione**. La creazione di sezioni duplicate è un ottimo modo per eseguire l'iterazione in una query senza perdere le iterazioni precedenti.

3. Per spostare una sezione verso l'alto in una cartella di lavoro, fare clic sull'icona **Sposta in alto** o **Sposta giù**.

4. Per rimuovere una sezione in modo permanente, fare clic sull'icona **Rimuovi**.

## <a name="adding-usage-data-visualization-sections"></a>Aggiunta di sezioni di visualizzazioni dei dati di utilizzo

Le cartelle di lavoro offrono quattro tipi di visualizzazioni di analisi di utilizzo predefiniti. Ogni tipo risponde a una domanda comune sull'utilizzo dell'app. Per aggiungere tabelle e grafici diversi da queste quattro sezioni, aggiungere sezioni di query di Analisi (vedere sotto).

Per aggiungere una sezione Utenti, Sessioni, Eventi o Conservazione alla cartella di lavoro, usare il pulsante **Aggiungi utenti** o un pulsante corrispondente nella parte inferiore della cartella di lavoro o di una sezione.

![Sezione Utenti nelle cartelle di lavoro](./media/app-insights-usage-workbooks/users-section.png)

Le sezioni **Utenti** rispondono alla domanda "Quanti utenti hanno visualizzato una pagina o usato una funzionalità del sito?"

Le sezioni **Sessioni** rispondono alla domanda "Per quante sessioni gli utenti hanno visualizzato una pagina o usato una funzionalità del sito?"

Le sezioni **Eventi** rispondono alla domanda "Quante volte gli utenti hanno visualizzato una pagina o usato una funzionalità del sito?"

Ognuno di questi tre tipi di sezione include gli stessi insiemi di controlli e visualizzazioni:

* Vedere le [informazioni sulla modifica delle sezioni Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
* Attivare o disattivare il grafico principale, le griglie degli istogrammi, le informazioni dettagliate automatiche e le visualizzazioni utenti di esempio usando le caselle di controllo **Mostra grafico**, **Mostra griglia**, **Mostra i dettagli** e **Esempio di questi utenti** nella parte superiore di ogni sezione.

![Sezione Conservazione nelle cartelle di lavoro](./media/app-insights-usage-workbooks/retention-section.png)

Le sezioni **Conservazione** rispondono alla domanda "Degli utenti che hanno visualizzato una pagina o usato una funzionalità in un determinato giorno o settimana quanti sono tornati un giorno o una settimana successiva?"

* Vedere le [informazioni sulla modifica delle sezioni Conservazione](app-insights-usage-retention.md)
* Attivare o disattivare il grafico Totale conservazione facoltativo usando la casella di controllo **Mostra il grafico generale della conservazione** nella parte superiore della sezione.

## <a name="adding-application-insights-analytics-sections"></a>Aggiunta di sezioni di analisi di Application Insights

![Sezione di analisi nelle cartelle di lavoro](./media/app-insights-usage-workbooks/analytics-section.png)

Per aggiungere una sezione di query di Analisi di Application Insights alla cartella di lavoro, usare il pulsante **Aggiungi query di analisi** nella parte inferiore della cartella di lavoro o nella parte inferiore di una sezione.

Le sezioni delle query di Analisi consentono di aggiungere query arbitrarie nei dati di Application Insights nelle cartelle di lavoro. Questa flessibilità consente di usare le sezioni di query di Analisi per rispondere a qualsiasi domanda sul sito diversa dalle quattro domande indicate in precedenza per le sezioni Utenti, Sessioni, Eventi e Conservazione, ad esempio:

* Quante eccezioni ha generato il sito nello stesso periodo di tempo come riduzione dell'utilizzo?
* Qual è stata la distribuzione dei tempi di caricamento della pagina per gli utenti che visualizzano una pagina?
* Quanti utenti hanno visualizzato un insieme di pagine nel sito ma non un altro insieme di pagine? Queste informazioni possono essere utili per comprendere se sono presenti cluster di utenti che usano sottoinsiemi diversi di funzionalità del sito (usare l'operatore `join` con il modificatore `kind=leftanti` nel linguaggio di query di Log Analytics).

Usare le [informazioni di riferimento sul linguaggio di query di Log Analytics](https://docs.loganalytics.io/) per altre informazioni sulla scrittura di query.

## <a name="adding-text-and-markdown-sections"></a>Aggiunta di sezioni di testo e Markdown

L'aggiunta di intestazioni, spiegazioni e commenti nelle cartelle di lavoro consente di trasformare un insieme di tabelle e grafici in un resoconto. Le sezioni di testo nelle cartelle di lavoro supportano la [sintassi Markdown](https://daringfireball.net/projects/markdown/) per la formattazione del testo, ad esempio intestazioni, grassetto, corsivo ed elenchi puntati.

Per aggiungere una sezione di testo alla cartella di lavoro, usare il pulsante **Aggiungi testo** nella parte inferiore della cartella di lavoro o nella parte inferiore di una sezione.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvataggio e condivisione delle cartelle di lavoro con il team

Le cartelle di lavoro vengono salvate all'interno di una risorsa di Application Insights, ovvero nella sezione **Report personali** privata o nella sezione **Report condivisi** accessibile a tutti gli utenti che hanno accesso alla risorsa di Application Insights. Per visualizzare tutte le cartelle di lavoro della risorsa, fare clic sul pulsante **Apri** nella barra delle azioni.

Per condividere una cartella di lavoro che è attualmente in **Report personali**:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da condividere
3. Fare clic su **Sposta in Report condivisi**.

Per condividere una cartella di lavoro con un collegamento o tramite posta elettronica, fare clic su **Condividi** nella barra delle azioni. Tenere presente che i destinatari del collegamento necessitano dell'accesso alla risorsa nel portale di Azure per visualizzare la cartella di lavoro. Per apportare modifiche, i destinatari devono avere almeno le autorizzazioni di collaboratori per la risorsa.

Per aggiungere un collegamento in una cartella di lavoro in un dashboard di Azure:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da aggiungere
3. Fare clic su **Aggiungi al dashboard**.

## <a name="next-steps"></a>Passaggi successivi

## <a name="next-steps"></a>Passaggi successivi
- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    - [Utenti, sessioni ed eventi](app-insights-usage-segmentation.md)
    - [Grafici a imbuto](usage-funnels.md)
    - [Conservazione](app-insights-usage-retention.md)
    - [Flussi degli utenti](app-insights-usage-flows.md)
    - [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)
    
