---
title: Analizzare i modelli di spostamento degli utenti con Flussi utente in Azure Application Insights | Microsoft Docs
description: "Analizzare il modo in cui gli utenti si spostano tra le pagine e le funzionalità dell'app Web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 63139bfb190959284cb5d25df1a8dd91bd7d913f
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analizzare i modelli di spostamento degli utenti con Flussi utente in Application Insights

![Strumento Flussi utente di Application Insights](./media/app-insights-usage-flows/00001-flows.png)

Lo strumento Flussi utente permette di visualizzare il modo in cui gli utenti si spostano tra le pagine e le funzionalità del sito. È utile per rispondere a domande simili alle seguenti:

* In che modo gli utenti lasciano una pagina del sito?
* Su quali elementi di una pagina del sito fanno clic gli utenti?
* In quali punti avviene più di frequente che gli utenti abbandonino il sito?
* Ci sono punti in cui gli utenti ripetono più volte la stessa azione?

Lo strumento Flussi utente parte da un'eccezione, un evento personalizzato o una visualizzazione pagina iniziale specificata. Una volta specificato questo evento iniziale, Flussi utente mostra gli eventi che si sono verificati prima e successivamente durante le sessioni utente. Le linee di spessore variabile mostrano quante volte ogni percorso è stato seguito dagli utenti. I nodi speciali **Sessione avviata** mostrano il punto in cui i nodi successivi hanno avviato una sessione. I nodi **Sessione terminata** mostrano il numero di utenti che non hanno inviato visualizzazioni pagina o eventi personalizzati dopo il nodo precedente, evidenziando il punto in cui probabilmente gli utenti hanno lasciato il sito.

> [!NOTE]
> Per poter usare lo strumento Flussi utente, la risorsa di Application Insights deve contenere visualizzazioni pagina o eventi personalizzati. Vedere le [informazioni su come configurare l'app per la raccolta automatica delle visualizzazioni pagina con Application Insights JavaScript SDK](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Scegliere un evento iniziale come punto di partenza

![Scegliere un evento iniziale per Flussi utente](./media/app-insights-usage-flows/00002-flows-initial-event.png)

Per iniziare a rispondere alle domande con lo strumento Flussi utente, scegliere un'eccezione, un evento personalizzato o una visualizzazione pagina iniziale da usare come punto di partenza per la visualizzazione:

1. Fare clic sul collegamento nel titolo **Operazioni eseguite dagli utenti dopo...?** oppure fare clic sul pulsante **Modifica**.
2. Selezionare una visualizzazione pagina, un evento personalizzato o un'eccezione nell'elenco a discesa **Evento iniziale**.
3. Fare clic su **Crea grafico**.

La colonna "Passaggio 1" della visualizzazione mostra cosa hanno fatto gli utenti con maggiore frequenza subito dopo l'evento iniziale, dalle azioni più frequenti alle meno frequenti ordinate dall'alto verso il basso. La colonna "Passaggio 2" e quelle successive mostrano cosa hanno fatto gli utenti successivamente, traducendo in un'immagine i diversi modi in cui gli utenti si sono spostati nel sito.

Per impostazione predefinita, lo strumento Flussi utente esegue campionamenti casuali solo delle ultime 24 ore di visualizzazioni pagina ed eventi personalizzati nel sito. È possibile aumentare l'intervallo di tempo e modificare il rapporto tra prestazioni e accuratezza per il campionamento casuale nel menu Modifica.

Se alcune delle visualizzazioni pagina, degli eventi personalizzati e delle eccezioni non sono rilevanti, fare clic sulla **X** nei nodi da nascondere. Dopo aver selezionato i nodi da nascondere, fare clic sul pulsante **Crea grafico** sotto la visualizzazione. Per vedere tutti i nodi nascosti, fare clic sul pulsante **Modifica** ed esaminare la sezione **Eventi esclusi**.

Se la visualizzazione non include visualizzazioni pagina o eventi personalizzati previsti, seguire questa procedura:

* Controllare la sezione **Eventi esclusi** del menu **Modifica**.
* Usare i pulsanti più (+) sui nodi **Altri** per includere eventi meno frequenti nella visualizzazione.
* Se la visualizzazione pagina o l'evento personalizzato previsto viene inviato raramente dagli utenti, provare ad aumentare l'intervallo di tempo della visualizzazione nel menu **Modifica**.
* Assicurarsi che la visualizzazione pagina, l'evento personalizzato o l'eccezione prevista sia configurata per la raccolta da parte di Application Insights SDK nel codice sorgente del sito. [Altre informazioni sulla raccolta di eventi personalizzati](app-insights-api-custom-events-metrics.md).

Per vedere più passaggi nella visualizzazione, usare gli elenchi a discesa **Passaggi precedenti** e **Passaggi successivi** sopra la visualizzazione.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Dopo aver visitato una pagina o una funzionalità, dove si spostano gli utenti e su quali elementi fanno clic?

![Usare Flussi utente per determinare su quali elementi fanno clic gli utenti](./media/app-insights-usage-flows/00003-flows-one-step.png)

Se l'evento iniziale è una visualizzazione pagina, la prima colonna ("Passaggio 1") della visualizzazione offre un modo rapido per capire cosa hanno fatto gli utenti immediatamente dopo aver visitato la pagina. Provare ad aprire il sito in una finestra accanto alla visualizzazione di Flussi utente. Confrontare le aspettative sull'interazione degli utenti con la pagina con l'elenco di eventi nella colonna "Passaggio 1". Spesso, un elemento dell'interfaccia utente della pagina che al team sembra insignificante può essere tra i più usati nella pagina. Può essere un ottimo punto di partenza per migliorare la progettazione del sito.

Se l'evento iniziale è un evento personalizzato, la prima colonna mostra cosa hanno fatto gli utenti subito dopo aver eseguito tale azione. Come con le visualizzazioni pagina, valutare se il comportamento osservato degli utenti corrisponde agli obiettivi e alle aspettative del team. Se l'evento iniziale selezionato è "Aggiunta elemento al carrello", ad esempio, verificare se "Vai alla cassa" e "Acquisto completato" compaiono poco dopo nella visualizzazione. Se il comportamento degli utenti è diverso rispetto alle aspettative, usare la visualizzazione per capire come aiutare gli utenti a districarsi nell'attuale progettazione del sito.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>In quali punti avviene più di frequente che gli utenti abbandonino il sito?

Cercare i nodi **Sessione terminata** mostrati nella parte alta delle colonne nella visualizzazione, in particolare all'inizio di un flusso. Ciò significa che molti utenti probabilmente hanno abbandonato il sito dopo aver seguito il percorso di pagine e interazioni dell'interfaccia utente precedente. In alcuni casi l'abbandono è previsto, ad esempio dopo aver completato un acquisto in un sito di e-commerce, ma in genere è segno di problemi di progettazione, prestazioni insoddisfacenti o altri aspetti del sito che è possibile migliorare.

Tenere presente che i nodi **Sessione terminata** si basano unicamente sui dati di telemetria raccolti da questa risorsa di Application Insights. Se Application Insights non riceve dati di telemetria per determinate interazioni dell'utente, è comunque possibile che gli utenti abbiano interagito con il sito in quei modo dopo il momento indicato come termine della sessione dallo strumento Flussi utente.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Ci sono punti in cui gli utenti ripetono più volte la stessa azione?

Cercare una visualizzazione pagina o un evento personalizzato ripetuto da molti utenti in passaggi successivi nella visualizzazione. In genere, ciò significa che gli utenti eseguono azioni ripetitive nel sito. In caso di azioni ripetitive, valutare la possibilità di modificare la progettazione del sito o di aggiungere nuove funzionalità per ridurre le ripetizioni. Ad esempio, aggiungere la funzionalità di modifica in blocco se gli utenti eseguono azioni ripetitive in ogni riga di un elemento tabella.

## <a name="common-questions"></a>Domande frequenti

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>L'evento iniziale rappresenta la prima volta che l'evento compare in una sessione oppure ogni volta che compare in una sessione?

L'evento iniziale nella visualizzazione rappresenta solo la prima volta che un utente ha inviato tale visualizzazione pagina o evento personalizzato durante una sessione. Se gli utenti possono inviare l'evento iniziale più volte in una sessione, la colonna "Passaggio 1" mostra solo il comportamento degli utenti dopo la *prima* istanza dell'evento iniziale, non dopo tutte le istanze.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Alcuni dei nodi della visualizzazione sono troppo generici. Ad esempio, è presente un nodo che indica solo "Pulsante selezionato". Come si può suddividere in nodi più dettagliati?

Usare le opzioni **Dividi per** nel menu **Modifica**:

1. Scegliere l'evento che si vuole suddividere nel menu **Evento**.
2. Scegliere una dimensione nel menu **Dimensione**. Se, ad esempio, è presente un evento denominato "Pulsante selezionato", provare con una proprietà personalizzata denominata "Nome pulsante".

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sull'uso](app-insights-usage-overview.md)
* [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
* [Conservazione](app-insights-usage-retention.md)
* [Aggiunta di eventi personalizzati all'app](app-insights-api-custom-events-metrics.md)