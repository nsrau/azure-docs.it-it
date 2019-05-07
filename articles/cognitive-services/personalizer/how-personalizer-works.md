---
title: Funzionamento di Personalizza esperienze - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Personalizza esperienze usa modelli di Machine Learning per individuare quale azione usare in un contesto. Ogni ciclo di apprendimento include un modello sottoposto a training esclusivamente con i dati inviati tramite chiamate a Classifica e a Ricompensa. Ogni ciclo di apprendimento è completamente indipendente rispetto agli altri.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025494"
---
# <a name="how-personalizer-works"></a>Funzionamento di Personalizza esperienze

Personalizza esperienze usa modelli di Machine Learning per individuare quale azione usare in un contesto. Ogni ciclo di apprendimento include un modello sottoposto a training esclusivamente con i dati inviati tramite chiamate a **Classifica** e a **Ricompensa**. Ogni ciclo di apprendimento è completamente indipendente rispetto agli altri. Creare un ciclo di apprendimento per ogni parte o comportamento dell'applicazione da personalizzare.

Per ogni ciclo, **chiamare l'API Classifica** in base al contesto corrente con:

* Elenco di possibili azioni: contenuti da cui selezionare l'azione principale.
* Elenco di [caratteristiche del contesto](concepts-features.md): dati contestualmente pertinenti come utente, contenuto e contesto.

L'API **Classifica** decide di usare:

* _Exploit_ (Sfruttamento): il modello corrente per decidere l'azione ottimale in base a dati passati.
* _Explore_ (Esplorazione): selezionare un'azione diversa invece di quella principale.

l'API **Ricompensa**:

* Raccoglie i dati per eseguire il training del modello registrando le caratteristiche e i punteggi di ricompensa di ogni chiamata a Classifica.
* Usa questi dati per aggiornare il modello in base alle impostazioni specificate in _Learning Policy_ (Criteri di apprendimento).

## <a name="architecture"></a>Architettura

L'immagine seguente mostra il flusso architetturale delle chiamate a Classifica e Ricompensa:

![testo alternativo](./media/how-personalizer-works/personalization-how-it-works.png "Funzionamento della personalizzazione")

1. Personalizza esperienze usa un modello interno di intelligenza artificiale per determinare la posizione in classifica dell'azione.
1. Il servizio decide se sfruttare il modello corrente o esplorare nuove scelte.  
1. Il risultato della classificazione viene inviato all'hub eventi.
1. Quando Personalizza esperienze riceve la ricompensa, la invia all'hub eventi. 
1. La classifica e la ricompensa sono correlate.
1. Il modello di intelligenza artificiale viene aggiornato in base ai risultati della correlazione.
1. Il motore di inferenza viene aggiornato con il nuovo modello. 

## <a name="research-behind-personalizer"></a>Ricerca alla base di Personalizza esperienze

Personalizza esperienze si basa su dati scientifici e ricerche nel campo dell'[apprendimento per rinforzo](concepts-reinforcement-learning.md), tra cui documenti, attività di ricerca e aree di studio in corso in Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Ciclo di apprendimento**: è possibile creare un ciclo di apprendimento per ogni parte dell'applicazione che potrà beneficiare dalla personalizzazione. Nel caso di più esperienze da personalizzare, creare un ciclo per ognuna. 

* **Azioni**: le azioni sono contenuti, come prodotti o promozioni, tra cui scegliere. Personalizza esperienze sceglie l'azione principale da mostrare agli utenti, la cosiddetta _azione ricompensa_, tramite l'API Classifica. Per ogni azione è possibile inviare caratteristiche con la richiesta Classifica.

* **Context**: per ottenere una classifica più accurata, fornire informazioni sul contesto, ad esempio:
    * L'utente.
    * Il dispositivo che usa. 
    * La data/ora corrente.
    * Altri dati sulla situazione corrente.
    * Dati cronologici sull'utente o sul contesto.

    Le specifiche applicazioni possono avere informazioni diverse sul contesto. 

* **[Caratteristiche](concepts-features.md)**: un'unità di informazioni su un contenuto o su un contesto utente.

* **Ricompensa**: la misura del tipo di risposta data dall'utente all'azione restituita dall'API Classifica, sotto forma di punteggio compreso tra 0 e 1. Il valore da 0 a 1 viene impostato dalla logica di business in base all'efficacia della scelta per realizzare gli obiettivi aziendali della personalizzazione. 

* **Esplorazione**: il servizio Personalizza esperienze usa l'esplorazione quando, invece di restituire l'azione ottimale, ne sceglie una diversa per l'utente. Il servizio Personalizza esperienze evita scenari di deriva e di stallo e può adattarsi al comportamento in corso dell'utente tramite esplorazione. 

* **Durata dell'esperimento**: il periodo di tempo durante il quale Personalizza esperienze aspetta una ricompensa, a partire dal momento in cui si verifica la chiamata a Classifica per tale evento.

* **Eventi inattivi**: un evento inattivo si verifica quando viene effettuata una chiamata a Classifica ma non è certo se l'utente vedrà il risultato a causa delle decisioni dell'applicazione client. Gli eventi inattivi consentono di creare e archiviare i risultati della personalizzazione e quindi di decidere di rimuoverli in seguito senza influire sul modello di Machine Learning.

* **Modello**: un modello di Personalizza esperienze acquisisce tutti i dati appresi sul comportamento dell'utente, ottenendo i dati di training dalla combinazione degli argomenti inviati alle chiamate a Classifica e Ricompensa e con un comportamento di training determinato dai criteri di apprendimento. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [casi in cui è possibile usare Personalizza esperienze](where-can-you-use-personalizer.md).