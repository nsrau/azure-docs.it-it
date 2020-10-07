---
title: Informazioni su Personalizza esperienze
description: Personalizza esperienze è un servizio basato sul cloud che consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizza esperienze, Personalizza esperienze di Azure, Machine Learning
ms.openlocfilehash: ae17b799c2b222525db53d5bb8e0afdbbcf19975
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777242"
---
# <a name="what-is-personalizer"></a>Informazioni su Personalizza esperienze

Personalizza esperienze di Azure è un servizio basato sul cloud che consente alle applicazioni di scegliere l'elemento di contenuto migliore da mostrare agli utenti. È possibile usare il servizio Personalizza esperienze per determinare il prodotto da suggerire ai clienti o per individuare la posizione ottimale per un annuncio pubblicitario. Una volta che il contenuto viene presentato all'utente, il sistema monitora il comportamento degli utenti in tempo reale e segnala un punteggio di ricompensa al servizio Personalizza esperienze. Questo garantisce il miglioramento continuo del modello di Machine Learning e la capacità di Personalizza esperienze di selezionare l'elemento di contenuto migliore in base alle informazioni contestuali ricevute.

> [!TIP]
> Il contenuto è qualsiasi unità di informazioni, ad esempio testo, immagini, URL, messaggi di posta elettronica o qualsiasi altro elemento da selezionare e mostrare agli utenti.

Prima di iniziare, è possibile provare [Personalizza esperienze con questa demo interattiva](https://personalizationdemo.azurewebsites.net/).

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>In che modo Personalizza esperienze seleziona l'elemento di contenuto migliore?

Personalizza esperienze usa l'**apprendimento per rinforzo** per selezionare l'elemento migliore (_azione_) in base al comportamento collettivo e ai punteggi di ricompensa in tutti gli utenti. Le azioni sono gli elementi di contenuto, ad esempio articoli di quotidiani, film specifici o prodotti.

La chiamata **Rank** acquisisce l'elemento azione, insieme alle relative caratteristiche, e le caratteristiche del contesto per selezionare l'elemento azione principale:

* **Azioni con caratteristiche**: elementi di contenuto con caratteristiche specifiche di ognuno
* **Caratteristiche del contesto**: caratteristiche degli utenti, del loro contesto o dell'ambiente quando usano l'app

La chiamata Rank restituisce l'ID dell'elemento di contenuto, ovvero l'__azione__, da mostrare all'utente, nel campo dell'**ID azione ricompensa**.

L'__azione__ mostrata all'utente viene scelta con modelli di Machine Learning, che provano a massimizzare la quantità totale di ricompense nel corso del tempo.

### <a name="sample-scenarios"></a>Scenari di esempio

Verranno ora esaminati alcuni scenari in cui è possibile usare Personalizza esperienze per selezionare il contenuto migliore da presentare a un utente.

|Tipo di contenuto|Azioni (con caratteristiche)|Caratteristiche del contesto|ID azione ricompensa restituito<br>(visualizza questo contenuto)|
|--|--|--|--|
|Elenco di notizie|a. `The president...` (nazionale, politica, [testo])<br>b. `Premier League ...` (globale, sport, [testo, immagine, video])<br> c. `Hurricane in the ...` (locale, meteo, [testo, immagine]|Notizie del dispositivo lette da<br>Mese o stagione<br>|a `The president...`|
|Elenco di film|1. `Star Wars` (1977, [azione, avventura, fantasy], George Lucas)<br>2. `Hoop Dreams` (1994, [documentario, sport], Steve James<br>3. `Casablanca` (1942, [commedia, genere drammatico, guerra], Michael Curtiz)|Film del dispositivo guardato da<br>Dimensioni dello schermo<br>Tipo di utente<br>|3. `Casablanca`|
|Elenco di prodotti|i. `Product A` (3 kg, €€€€, consegna in 24 ore)<br>ii. `Product B` (20 kg, €€, spedizione in 2 settimane con dogana)<br>iii. `Product C` (3 kg, €€€, consegna in 48 ore)|Acquisti del dispositivo letti da<br>Livello di spesa dell'utente<br>Mese o stagione|ii. `Product B`|

Personalizza esperienze usa l'apprendimento per rinforzo per selezionare la singola azione migliore, nota come _ID azione ricompensa_. Il modello di Machine Learning usa: 

* Un modello con training: informazioni ricevute in precedenza dal servizio di personalizzazione usato per migliorare il modello di Machine Learning
* Dati correnti: azioni specifiche con caratteristiche e caratteristiche del contesto

## <a name="when-to-use-personalizer"></a>In quali situazioni usare Personalizza esperienze

L'**API** [Classificazione](https://go.microsoft.com/fwlink/?linkid=2092082) di Personalizza esperienze viene chiamata ogni volta che l'applicazione presenta contenuto. Questo scenario è noto come **evento**, contrassegnato con un _ID evento_.

L'[API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **Premio** di Personalizza esperienze può essere chiamata in tempo reale o ritardata in base ai requisiti dell'infrastruttura. Il punteggio di ricompensa viene determinato in base alle esigenze aziendali. Il valore è compreso tra 0 e 1. Può trattarsi di un valore singolo, ad esempio 1 per positivo e 0 per negativo, o di un numero prodotto da un algoritmo creato considerando gli obiettivi e le metriche aziendali.

## <a name="content-requirements"></a>Requisiti per il contenuto

Usare Personalizza esperienze quando il contenuto:

* Include un set limitato di elementi (massimo circa 50) tra cui selezionare. Se l'elenco è più lungo, [usare un motore di raccomandazioni](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) per ridurlo a 50 elementi.
* Include informazioni che descrivono il contenuto da classificare: _azioni con caratteristiche_ e _caratteristiche del contesto_.
* Include almeno circa 1000 eventi correlati al contenuto al giorno affinché Personalizza esperienze sia efficace. Se Personalizza esperienze non riceve il traffico minimo richiesto, il servizio impiega più tempo a determinare il singolo elemento di contenuto migliore.

Poiché Personalizza esperienze usa informazioni collettive quasi in tempo reale per restituire il singolo elemento di contenuto migliore, il servizio non:
* Gestisce e rende persistenti le informazioni sul profilo utente
* Registra le preferenze o la cronologia dei singoli utenti
* Richiede contenuto etichettato e pulito

## <a name="how-to-design-for-and-implement-personalizer"></a>Come progettare e implementare Personalizza esperienze

1. [Progettare](concepts-features.md) e pianificare il contenuto, le **_azioni_** e il **_contesto_**. Determinare l'algoritmo di ricompensa per il punteggio di **_ricompensa_**.
1. Ogni risorsa di [Personalizza esperienze](how-to-settings.md) creata viene considerata un ciclo di apprendimento. Il ciclo riceverà le chiamate a Rank e Reward per tale contenuto o esperienza utente.

    |Tipo di risorsa| Scopo|
    |--|--|
    |[Modalità Apprentice](concept-apprentice-mode.md) `E0`|Eseguire il training del modello di Personalizza esperienze senza alcun impatto sull'applicazione esistente, quindi distribuire il comportamento di apprendimento online in un ambiente di produzione|
    |Standard, `S0`|Comportamento di apprendimento online in un ambiente di produzione|
    |Libero, `F0`| Provare il comportamento di apprendimento online in un ambiente non di produzione|

1. Aggiungere Personalizza esperienze all'applicazione, al sito Web o al sistema:
    1. Aggiungere una chiamata **Rank** a Personalizza esperienze nell'applicazione, nel sito Web o nel sistema per determinare il singolo elemento di _contenuto_ migliore prima che il contenuto venga mostrato all'utente.
    1. Mostrare all'utente il singolo elemento di _contenuto_ migliore, che corrisponde all'_ID azione ricompensa_ restituito.
    1. Applicare la _logica aziendale_ alle informazioni raccolte sul comportamento passato dell'utente per determinare il punteggio di **ricompensa**, ad esempio:

    |Comportamento|Punteggio di ricompensa calcolato|
    |--|--|
    |L'utente ha selezionato il singolo elemento di _contenuto_ migliore (ID azione ricompensa)|**1**|
    |L'utente ha selezionato altro contenuto|**0**|
    |L'utente è in pausa e indeciso prima di selezionare il singolo elemento di _contenuto_ migliore (ID azione ricompensa)|**0,5**|

    1. Aggiungere una chiamata **Reward** per l'invio di un punteggio di ricompensa compreso tra 0 e 1
        * Immediatamente dopo aver mostrato il contenuto
        * Oppure più tardi in un sistema offline
    1. [Valutare il ciclo](concepts-offline-evaluation.md) con una valutazione offline dopo un periodo di utilizzo. La valutazione offline consente di testare e valutare l'efficacia del servizio Personalizza esperienze senza cambiare il codice o influire sull'esperienza utente.

## <a name="complete-a-quickstart"></a>Completare una guida di avvio rapido

Sono disponibili le guide di avvio rapido per C#, JavaScript e Python. Ognuna di esse è progettata per insegnare gli schemi progettuali di base e consentire all'utente di eseguire il codice in meno di 10 minuti. 

* [Avvio rapido: Come usare la libreria client di Personalizza esperienze](sdk-learning-loop.md)

Una volta acquisita familiarità con il servizio Personalizza esperienze, è provare le esercitazioni e imparare a usare Personalizza esperienze in applicazioni Web, chatbot o notebook di Azure.

* [Esercitazione: Usare Personalizza esperienze in un'app Web .NET](tutorial-use-personalizer-web-app.md)
* [Esercitazione: Usare Personalizza esperienze in un chatbot .NET](tutorial-use-personalizer-chat-bot.md)
* [Esercitazione: Usare Personalizza esperienze con un notebook di Azure](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>Informazioni di riferimento 

* [Personalizer C#/.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Personalizer Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/personalizer/v1.0/personalizer)
* [Personalizer JavaScript SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Personalizer Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come funziona Personalizza esperienze](how-personalizer-works.md)
> [Cos'è l'apprendimento per rinforzo?](concepts-reinforcement-learning.md)
