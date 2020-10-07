---
title: Dove e come usare-Customizzator
description: Personalizza esperienze può essere applicato in qualsiasi situazione in cui l'applicazione può selezionare l'elemento, l'azione o il prodotto giusto da visualizzare, per migliorare l'esperienza, ottenere risultati aziendali migliori o migliorare la produttività.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777225"
---
# <a name="where-and-how-to-use-personalizer"></a>Dove e come usare il Personalizzatore

Usare la funzionalità di personalizzazione in ogni situazione in cui l'applicazione deve selezionare l'azione corretta (contenuto) da visualizzare, in modo da migliorare l'esperienza, ottenere risultati aziendali migliori o migliorare la produttività.

Il Personalizzatore usa l'apprendimento per rinforzo per selezionare l'azione (contenuto) per visualizzare l'utente. La selezione può variare notevolmente a seconda della quantità, della qualità e della distribuzione dei dati inviati al servizio.

## <a name="example-use-cases-for-personalizer"></a>Casi d'uso di esempio per Personalizza esperienze

* **Chiarimento preventivo & risoluzione dell'ambiguità**: aiutare gli utenti ad avere un'esperienza migliore quando il loro scopo non è chiaro offrendo un'opzione personalizzata.
* **Suggerimenti predefiniti** per i menu & opzioni: il bot suggerisce l'elemento più probabile in modo personalizzato come primo passaggio, anziché presentare un menu impersonale o un elenco di alternative.
* **Tratti di bot & tono**: per i bot che possono variare il tono, il livello di dettaglio e lo stile di scrittura, provare a variare questi tratti.
* **Notifica & contenuto dell'avviso**: decidere quale testo utilizzare per gli avvisi per coinvolgere altri utenti.
* **Notifica & intervallo di avvisi**: l'apprendimento personalizzato di quando inviare notifiche agli utenti per inserirli più.


## <a name="expectations-required-to-use-personalizer"></a>Aspettative richieste per l'uso del personalizzatore

È possibile applicare la personalizzazione nei casi in cui si soddisfino o si possa implementare le linee guida seguenti.

|Linee guida|Spiegazione|
|--|--|
|Obiettivo aziendale|Si ha un obiettivo aziendale o di usabilità per l'applicazione.|
|Contenuto|Decidere contestualmente cosa mostrare agli utenti in un determinato punto dell'applicazione migliora tale obiettivo.|
|Quantità di contenuto|Sono presenti meno di 50 azioni di rango per chiamata.|
|Dati aggregati|La scelta migliore può e deve essere appresa in base al comportamento collettivo degli utenti e al punteggio totale.|
|Uso etico|L'uso dell'apprendimento automatico per la personalizzazione segue le [linee guida per l'uso responsabile](ethics-responsible-use.md) e le scelte personali.
|Opzione singola migliore|La decisione contestuale può essere espressa classificando la migliore opzione (azione) in un set di scelte limitato.|
|Risultato con Punteggio|Il modo in cui la scelta di classificazione è stata elaborata per l'applicazione può essere determinata dalla misurazione di un aspetto del comportamento dell'utente e dall'espressa in un _[Punteggio di ricompensa](concept-rewards.md)_.|
|Temporizzazione pertinente|Il punteggio non introduce troppi fattori esterni o di confusione. La durata dell'esperimento è abbastanza ridotta da poter calcolare il punteggio finché è ancora pertinente.|
|Funzionalità di contesto sufficienti|È possibile esprimere il contesto per l'ordine di priorità come un elenco di almeno 5 [funzioni](concepts-features.md) che si ritiene possano essere utili per la scelta corretta e che non includa informazioni identificabili specifiche dell'utente.|
|Funzionalità di azione sufficienti|Sono disponibili informazioni su ciascuna scelta di contenuto, _azione_, come elenco di almeno 5 [funzioni](concepts-features.md) che si ritiene aiutino Personalizza esperienze a operare la scelta giusta.|
|Dati giornalieri|Ci sono eventi sufficienti a tenere sotto controllo la personalizzazione ottimale se il problema devia nel corso del tempo (ad esempio, le preferenze nel campo delle notizie o della moda). Personalizza esperienze si adatta ai cambiamenti continui del mondo reale, ma i risultati non saranno ottimali se non ci sono eventi e dati sufficienti da cui apprendere per individuare e stabilire nuovi criteri. È consigliabile scegliere un caso d'uso che accada abbastanza frequentemente. Prendere in considerazione la ricerca di casi d'uso che si verifichino almeno 500 volte al giorno.|
|Dati cronologici|L'applicazione può conservare dati per il tempo sufficiente ad accumulare una cronologia di almeno 100.000 interazioni. In questo modo, il personale potrà raccogliere dati sufficienti per eseguire valutazioni offline e l'ottimizzazione dei criteri.|

**Non usare il Personalizzatore** in cui il comportamento personalizzato non è un elemento che può essere individuato in tutti gli utenti. Può ad esempio essere utile usare Personalizza esperienze per suggerire la pizza da ordinare tra un elenco di 20 possibili tipi, ma la scelta del contatto da chiamare quando si ha bisogno di aiuto per i bambini (come "Nonna") non è un'opzione personalizzabile per un'intera base di utenti.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Come usare Personalizza esperienze in un'applicazione Web

L'aggiunta di un ciclo di apprendimento a un'applicazione Web include:

* Determinare l'esperienza da personalizzare, le azioni e le funzionalità disponibili, le caratteristiche del contesto da usare e la ricompensa da impostare.
* Aggiungere nell'applicazione un riferimento all'SDK per la personalizzazione.
* Chiamare l'API per la classificazione quando si è pronti ad avviare la personalizzazione.
* Memorizzare l'ID dell'evento. La ricompensa tramite l'apposita API viene inviata in un secondo momento.
1. Chiamare la funzione di attivazione per l'evento quando si è certi che l'utente ha visualizzato la pagina personalizzata.
1. Attendere che l'utente esegua la selezione del contenuto classificato.
1. Chiamare l'API per la ricompensa per specificare l'efficacia dell'output dell'API per la classificazione.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Come usare Personalizza esperienze con un chatbot

In questo esempio si vedrà come usare Personalizza esperienze in modo da inviare un suggerimento predefinito anziché inviare ogni volta all'utente una serie di opzioni o menu.

* Scaricare il [codice](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) per questo esempio.
* Configurare la soluzione del bot. Assicurarsi di pubblicare l'applicazione LUIS.
* Gestire per il bot le chiamate alle API per la classificazione e la ricompensa.
    * Aggiungere codice per gestire l'elaborazione della finalità di LUIS. Se come finalità principale viene restituito **None** o il punteggio della finalità risulta al di sotto della soglia della logica di business, inviare l'elenco delle finalità a Personalizza esperienze per classificarle.
    * Mostrare all'utente l'elenco delle finalità come collegamenti selezionabili, dove la prima finalità corrisponde a quella classificata con priorità più alta dalla risposta dell'API.
    * Acquisire la selezione dell'utente e includerla nella chiamata all'API per la ricompensa.

### <a name="recommended-bot-patterns"></a>Modelli consigliati per il bot

* Eseguire chiamate all'API per la classificazione di Personalizza esperienze ogni volta che è necessaria una disambiguazione, invece di memorizzare nella cache i risultati per ogni utente. Il risultato della disambiguazione della finalità può cambiare nel tempo per una stessa persona. Consentendo all'API per la classificazione di esplorare le varianze si accelererà l'apprendimento complessivo.
* Scegliere un'interazione comune a molti utenti in modo da avere dati sufficienti per la personalizzazione. Ad esempio, le domande introduttive possono essere più appropriate rispetto ai piccoli chiarimenti introdotti più avanti nella conversazione, che potrebbero essere visualizzati solo da alcuni utenti.
* Usare chiamate all'API per la classificazione per abilitare conversazioni di tipo "il primo suggerimento è quello giusto", in cui all'utente viene chiesto "Vuoi X?" o "Intendi dire X?" e l'utente può solo confermare, anziché avere la possibilità di scegliere più opzioni da un menu. Ad esempio, User: "Mi piacerebbe ordinare un caffè" bot: "si vuole un doppio espresso?". In questo modo, anche il segnale di ricompensa è forte poiché fa direttamente riferimento all'unico suggerimento fornito.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Come usare Personalizza esperienze con una soluzione di raccomandazione

Molte aziende usano motori di raccomandazione, strumenti di marketing e conduzione di campagne, segmentazione e clustering del pubblico, filtri collaborativi e altri mezzi per consigliare prodotti da un ampio catalogo ai clienti.

Il [repository GitHub di Microsoft recommenders](https://github.com/Microsoft/Recommenders) fornisce esempi e procedure consigliate per la creazione di sistemi di raccomandazione, forniti come notebook di Jupyter. Fornisce esempi funzionanti per preparare dati, compilare modelli, valutare, ottimizzare e rendere operativi i motori di raccomandazione, per molti approcci comuni, tra cui xDeepFM, SAR, ALS, RBM e DKN.

Personalizza esperienze può funzionare con un motore di raccomandazione, se presente.

* I motori di raccomandazione richiedono grandi quantità di elementi (ad esempio, 500.000) e raccomandano un subset (ad esempio i primi 20) da centinaia o migliaia di opzioni.
* Personalizza esperienze accetta un numero ridotto di azioni con una grande quantità di informazioni su di esse e le classifica in tempo reale per un determinato contenuto avanzato, mentre la maggior parte dei motori di raccomandazione usano solo alcuni attributi riguardanti gli utenti, i prodotti e le relative interazioni.
* Personalizza esperienze è progettato per esplorare continuamente e in modo autonomo le preferenze dell'utente, restituendo risultati migliori nei casi in cui il contenuto cambia rapidamente, ad esempio notizie, eventi in tempo reale, contenuti live della community, contenuto con aggiornamenti quotidiani o contenuti stagionali.

Un uso comune consiste nell'usare l'output di un motore di raccomandazione (ad esempio, i primi 20 prodotti per un determinato cliente) come azioni di input per Personalizza esperienze.

## <a name="adding-content-safeguards-to-your-application"></a>Aggiungere all'applicazione misure di sicurezza per il contenuto

Se l'applicazione consente la visualizzazione di ampie varianze di contenuto e parte di tale contenuto può essere poco sicuro o risultare inappropriato per alcuni utenti, è consigliabile controllare in anticipo che vengano implementate le opportune misure di sicurezza per impedire agli utenti di visualizzare contenuto inaccettabile. Il modello migliore per implementare le misure di sicurezza è:
    * Ottenere l'elenco delle azioni da classificare.
    * Escludere quelle che non sono attuabili per i destinatari.
    * Classificare solo le azioni attuabili.
    * Mostrare all'utente l'azione con la priorità più alta.

In alcune architetture la sequenza precedente può risultare difficile da implementare. In tal caso, esiste un approccio alternativo all'implementazione di misure di sicurezza dopo la classificazione, ma è necessario eseguire un provisioning, in modo che le azioni che esulano dalla protezione non vengano utilizzate per eseguire il training del modello di personalizzazione.

* Ottenere l'elenco delle azioni da classificare, con apprendimento disattivato.
* Classificare le azioni in ordine di priorità.
* Controllare se l'azione con priorità più alta è attuabile.
    * In caso affermativo, attivare l'apprendimento per questo livello di classificazione e quindi mostrarlo all'utente.
    * Se l'azione con priorità più alta non è attuabile, non attivare l'apprendimento per questa classificazione e decidere cosa mostrare all'utente in base alla propria logica o ad approcci alternativi. Anche se si usa la seconda opzione migliore, non attivare l'apprendimento per questa classificazione.


## <a name="next-steps"></a>Passaggi successivi

[Etica e uso responsabile](ethics-responsible-use.md).
