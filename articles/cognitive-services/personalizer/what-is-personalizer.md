---
title: Informazioni su Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Personalizza esperienze è un servizio API basato sul cloud che consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 756363d0c46dee6f7d0037fda48ab22dbdaeb0b0
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514304"
---
# <a name="what-is-personalizer"></a>Informazioni su Personalizza esperienze

Personalizza esperienze di Azure è un servizio API basato sul cloud che consente all'applicazione client di scegliere il singolo elemento di _contenuto_ migliore da mostrare a ogni utente. Il servizio seleziona l'elemento migliore, da elementi di contenuto, in base all'insieme di informazioni in tempo reale fornite in merito al contenuto e al contesto.

Dopo aver presentato l'elemento di contenuto all'utente, il sistema monitora il comportamento dell'utente e segnala un punteggio di ricompensa a Personalizza esperienze per migliorarne la capacità di selezionare il contenuto migliore in base alle informazioni di contesto ricevute.

Il **contenuto** può essere qualsiasi unità di informazioni, ad esempio testo, immagini, URL o messaggi di posta elettronica, da selezionare e mostrare all'utente.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>In che modo Personalizza esperienze seleziona l'elemento di contenuto migliore?

Personalizza esperienze usa l'**apprendimento per rinforzo** per selezionare l'elemento migliore (_azione_) in base al comportamento collettivo e ai punteggi di ricompensa in tutti gli utenti. Le azioni sono elementi di contenuto, ad esempio articoli di notizie, film specifici o prodotti tra cui scegliere.

La chiamata **Rank** acquisisce l'elemento azione, insieme alle relative caratteristiche, e le caratteristiche del contesto per selezionare l'elemento azione principale:

* **Azioni con caratteristiche**: elementi di contenuto con caratteristiche specifiche di ognuno
* **Caratteristiche del contesto**: caratteristiche degli utenti, del loro contesto o dell'ambiente quando usano l'app

La chiamata Rank restituisce l'ID dell'elemento di contenuto, ovvero l'__azione__, da mostrare all'utente, nel campo dell'**ID azione ricompensa**.
L'__azione__ mostrata all'utente viene scelta con modelli di Machine Learning, provando a massimizzare la quantità totale di ricompense nel corso del tempo.

Ecco diversi scenari di esempio:

|Tipo di contenuto|**Azioni (con caratteristiche)**|**Caratteristiche del contesto**|ID azione ricompensa restituito<br>(visualizza questo contenuto)|
|--|--|--|--|
|Elenco di notizie|a. `The president...` (nazionale, politica, [testo])<br>b. `Premier League ...` (globale, sport, [testo, immagine, video])<br> c. `Hurricane in the ...` (locale, meteo, [testo, immagine]|Notizie del dispositivo lette da<br>Mese o stagione<br>|a `The president...`|
|Elenco di film|1. `Star Wars` (1977, [azione, avventura, fantasy], George Lucas)<br>2. `Hoop Dreams` (1994, [documentario, sport], Steve James<br>3. `Casablanca` (1942, [commedia, genere drammatico, guerra], Michael Curtiz)|Film del dispositivo guardato da<br>Dimensioni dello schermo<br>Tipo di utente<br>|3. `Casablanca`|
|Elenco di prodotti|i. `Product A` (3 kg, €€€€, consegna in 24 ore)<br>ii. `Product B` (20 kg, €€, spedizione in 2 settimane con dogana)<br>iii. `Product C` (3 kg, €€€, consegna in 48 ore)|Acquisti del dispositivo letti da<br>Livello di spesa dell'utente<br>Mese o stagione|ii. `Product B`|

Personalizza esperienze usa l'apprendimento per rinforzo per selezionare la singola azione migliore, nota come _ID azione ricompensa_, in base a una combinazione di:
* Modello sottoposto a training: informazioni passate ricevute da Personalizza esperienze
* Dati correnti: azioni specifiche con caratteristiche e caratteristiche del contesto

## <a name="when-to-call-personalizer"></a>Quando chiamare Personalizza esperienze

L'[API](https://go.microsoft.com/fwlink/?linkid=2092082) **Classificazione** di Personalizza esperienze viene chiamata _ogni volta_ che si presenta contenuto, in tempo reale. Questo scenario è noto come **evento**, contrassegnato con un _ID evento_.

L'[API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **Premio** di Personalizza esperienze può essere chiamata in tempo reale o ritardata in base ai requisiti dell'infrastruttura. Il punteggio di ricompensa viene determinato in base alle esigenze aziendali. Può trattarsi di un valore singolo, ad esempio 1 per positivo e 0 per negativo, o di un numero prodotto da un algoritmo creato considerando gli obiettivi e le metriche aziendali.

## <a name="personalizer-content-requirements"></a>Requisiti del contenuto di Personalizza esperienze

Usare Personalizza esperienze quando il contenuto:

* Include un set limitato di elementi (massimo circa 50) tra cui selezionare. Se l'elenco è più lungo, [usare un motore di raccomandazioni](where-can-you-use-personalizer.md#use-personalizer-with-recommendation-engines) per ridurlo a 50 elementi.
* Include informazioni che descrivono il contenuto da classificare: _azioni con caratteristiche_ e _caratteristiche del contesto_.
* Include almeno circa 1000 eventi correlati al contenuto al giorno affinché Personalizza esperienze sia efficace. Se Personalizza esperienze non riceve il traffico minimo richiesto, il servizio impiega più tempo a determinare il singolo elemento di contenuto migliore.

Poiché Personalizza esperienze usa informazioni collettive quasi in tempo reale per restituire il singolo elemento di contenuto migliore, il servizio non:
* Gestisce e rende persistenti le informazioni sul profilo utente
* Registra le preferenze o la cronologia dei singoli utenti
* Richiede contenuto etichettato e pulito

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Come progettare e implementare Personalizza esperienze per l'applicazione client

1. [Progettare](concepts-features.md) e pianificare il contenuto, le **_azioni_** e il **_contesto_** . Determinare l'algoritmo di ricompensa per il punteggio di **_ricompensa_** .
1. Ogni risorsa di [Personalizza esperienze](how-to-settings.md) creata viene considerata 1 ciclo di apprendimento. Il ciclo riceverà le chiamate a Rank e Reward per tale contenuto o esperienza utente.
1. Aggiungere Personalizza esperienze al sito Web o al sistema di contenuto:
    1. Aggiungere una chiamata **Rank** a Personalizza esperienze nell'applicazione, nel sito Web o nel sistema per determinare il singolo elemento di _contenuto_ migliore prima che il contenuto venga mostrato all'utente.
    1. Mostrare all'utente il singolo elemento di _contenuto_ migliore, che corrisponde all'_ID azione ricompensa_ restituito.
    1. Applicare l'_algoritmo_ alle informazioni raccolte sul comportamento passato dell'utente per determinare il punteggio di **ricompensa**, ad esempio:

        |Comportamento|Punteggio di ricompensa calcolato|
        |--|--|
        |L'utente ha selezionato il singolo elemento di _contenuto_ migliore (ID azione ricompensa)|**1**|
        |L'utente ha selezionato altro contenuto|**0**|
        |L'utente è in pausa e indeciso prima di selezionare il singolo elemento di _contenuto_ migliore (ID azione ricompensa)|**0,5**|

    1. Aggiungere una chiamata **Reward** per l'invio di un punteggio di ricompensa compreso tra 0 e 1
        * Immediatamente dopo aver mostrato il contenuto
        * Oppure più tardi in un sistema offline
    1. [Valutare il ciclo](concepts-offline-evaluation.md) con una valutazione offline dopo un periodo di utilizzo. La valutazione offline consente di testare e valutare l'efficacia del servizio Personalizza esperienze senza cambiare il codice o influire sull'esperienza utente.

## <a name="next-steps"></a>Passaggi successivi


* [Funzionamento di Personalizza esperienze](how-personalizer-works.md)
* Vedere le informazioni su [cos'è l'apprendimento per rinforzo](concepts-reinforcement-learning.md)
* [Informazioni sulle funzionalità e le azioni per la richiesta di classificazione](concepts-features.md)
* [Informazioni su come determinare il punteggio per la richiesta di ricompensa](concept-rewards.md)
* [Guide introduttive]()
* [Esercitazione]()
* [Usare la demo interattiva](https://personalizationdemo.azurewebsites.net/)
