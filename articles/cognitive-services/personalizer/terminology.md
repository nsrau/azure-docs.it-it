---
title: Terminologia - Personalizer
description: Personalizer utilizza la terminologia dell'apprendimento dei rinforzi. Questi termini vengono usati nel portale di Azure e nelle API.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624275"
---
# <a name="terminology"></a>Terminologia

Personalizer utilizza la terminologia dell'apprendimento dei rinforzi. Questi termini vengono usati nel portale di Azure e nelle API.

## <a name="conceptual-terminology"></a>Terminologia concettuale

* **Learning Loop**: Si crea una risorsa Personalizer, denominata _learning loop_, per ogni parte dell'applicazione che può trarre vantaggio dalla personalizzazione. Nel caso di più esperienze da personalizzare, creare un ciclo per ognuna.

* **Modello:** un modello Personalizer acquisisce tutti i dati appresi sul comportamento dell'utente, ottenendo i dati di training dalla combinazione degli argomenti inviati alle chiamate Rank e Reward e con un comportamento di training determinato dai criteri di apprendimento.

## <a name="personalizer-configuration"></a>Configurazione del personalista

Il programma di configurazione è configurato dal portale di [Azure.](https://portal.azure.com)

* **Ricompense:** configurare i valori predefiniti per il tempo di attesa del premio, il premio predefinito e i criteri di aggregazione dei premi.

* **Esplorazione**: configurare la percentuale di chiamate Rank da utilizzare per l'esplorazione

* **Frequenza di aggiornamento**del modello : Frequenza di rievaglio del modello.

* **Conservazione dei dati**: Quanti giorni vale la pena di dati da memorizzare. Ciò può influire sulle valutazioni offline, che vengono utilizzate per migliorare il ciclo di apprendimento.

## <a name="use-rank-and-reward-apis"></a>Usa le API Rank e Reward

* **Classificazione**: Date le azioni con le funzionalità e le funzioni di contesto, utilizzare esplorare o sfruttare per restituire l'azione superiore (elemento di contenuto).

    * **Azioni**: Le azioni sono gli elementi di contenuto, ad esempio prodotti o promozioni, tra cui scegliere. Personalizer sceglie l'azione principale (ID azione premio restituito) da mostrare agli utenti tramite l'API Rank.

    * **Contesto**: Per fornire una classificazione più accurata, fornire informazioni sul contesto, ad esempio:
        * L'utente.
        * Il dispositivo che usa.
        * Ora corrente.
        * Altri dati sulla situazione corrente.
        * Dati cronologici sull'utente o sul contesto.

        Le specifiche applicazioni possono avere informazioni diverse sul contesto.

    * **[Caratteristiche](concepts-features.md)**: un'unità di informazioni su un elemento di contenuto o un contesto utente. Assicurarsi di utilizzare solo le funzionalità aggregate. Non usare orari specifici, ID utente o altri dati non aggregati come funzionalità.

        * Una funzionalità di _azione_ sono i metadati relativi al contenuto.
        * Una funzionalità di _contesto_ è un metadata del contesto in cui viene presentato il contenuto.

* **Esplorazione**: Il servizio Personalizer esplora quando, invece di restituire l'azione migliore, sceglie un'azione diversa per l'utente. Il servizio Personalizza esperienze evita scenari di deriva e di stallo e può adattarsi al comportamento in corso dell'utente tramite esplorazione.

* **Sfruttamento**: Il servizio Personalizer utilizza il modello corrente per decidere l'azione migliore in base ai dati passati.

* **Durata dell'esperimento**: il tempo di attesa di una ricompensa da parte del servizio Personalizer, a partire dal momento in cui si è verificata la chiamata di classificazione per quell'evento.

* **Eventi inattivi**: Un evento inattivo è quello in cui è stato chiamato Rank, ma non si è sicuri che l'utente vedrà mai il risultato, a causa delle decisioni dell'applicazione client. Gli eventi inattivi consentono di creare e archiviare i risultati della personalizzazione e quindi di decidere di rimuoverli in seguito senza influire sul modello di Machine Learning.


* **Ricompensa**: Una misura del modo in cui l'utente ha risposto all'ID azione premio restituito dall'API di classificazione, come punteggio compreso tra 0 e 1. Il valore da 0 a 1 viene impostato dalla logica di business in base all'efficacia della scelta per realizzare gli obiettivi aziendali della personalizzazione. Il ciclo di apprendimento non memorizza questa ricompensa come cronologia dei singoli utenti.

## <a name="offline-evaluations"></a>Valutazioni offline

* **Valutazione**: Una valutazione offline determina i criteri di apprendimento migliori per il ciclo in base ai dati del ciclo.

* **Politica di apprendimento**: Il modo in cui Personalizer elabora un modello in ogni evento verrà determinato da alcuni parametri che influiscono sul funzionamento dell'algoritmo di apprendimento automatico. Un nuovo ciclo di apprendimento inizia con una politica di **apprendimento**predefinita, che può produrre prestazioni moderate. Quando si esegue [Valutazioni](concepts-offline-evaluation.md), Personalizer crea nuovi criteri di apprendimento specificamente ottimizzati per i casi di utilizzo del ciclo. Personalizer eseguirà prestazioni significativamente migliori con criteri ottimizzati per ogni ciclo specifico, generato durante la valutazione. I criteri di _apprendimento_ sono denominati impostazioni di apprendimento nelle impostazioni modello e apprendimento per la risorsa Personalizer nel portale di Azure.The learning policy is named learning settings on the Model and learning **settings** for the Personalizer resource in the Azure portal.