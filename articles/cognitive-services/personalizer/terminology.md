---
title: Terminologia-personalizzatore
description: Il Personalizzatore usa la terminologia dell'apprendimento per rinforzo. Questi termini vengono usati nell'portale di Azure e nelle API.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 8177606ac6e968bd287a23554be7b9dd06d880a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002863"
---
# <a name="personalizer-terminology"></a>Terminologia del personalizzatore

Il Personalizzatore usa la terminologia dell'apprendimento per rinforzo. Questi termini vengono usati nell'portale di Azure e nelle API.

## <a name="conceptual-terminology"></a>Terminologia concettuale

* **Learning loop**: si crea una risorsa di personalizzazione, denominata _ciclo di apprendimento_, per ogni parte dell'applicazione che può trarre vantaggio dalla personalizzazione. Nel caso di più esperienze da personalizzare, creare un ciclo per ognuna.

* **Modello**: un modello di personalizzatore acquisisce tutti i dati appresi sul comportamento dell'utente, recuperando i dati di training dalla combinazione degli argomenti inviati a chiamate di rango e premi e con un comportamento di training determinato dai criteri di apprendimento.

* **Modalità online**: il [comportamento di apprendimento](#learning-behavior) predefinito per il Personalizzatore in cui il ciclo di apprendimento USA Machine Learning per compilare il modello che consente di stimare l' **azione principale** per il contenuto.

* **Modalità Apprentice**: un [comportamento di apprendimento](#learning-behavior) che consente di avviare un modello di personalizzazione per eseguire il training senza alcun effetto sui risultati e sulle azioni delle applicazioni.

## <a name="learning-behavior"></a>Comportamento di apprendimento:

* **Modalità online**: restituisce l'azione migliore. Il modello risponderà alle chiamate di rango con l'azione migliore e utilizzerà le chiamate Reward per apprendere e migliorare le selezioni nel tempo.
* **[Modalità apprendista](concept-apprentice-mode.md)**: Impara come apprendistato. Il modello apprenderà osservando il comportamento del sistema esistente. Le chiamate di rango restituiranno sempre l' **azione predefinita** dell'applicazione (baseline).

## <a name="personalizer-configuration"></a>Configurazione della personalizzazione

Il Personalizzatore viene configurato dal [portale di Azure](https://portal.azure.com).

* **Ricompense**: configurare i valori predefiniti per il tempo di attesa della ricompensa, la ricompensa predefinita e i criteri di aggregazione Reward.

* **Esplorazione**: configurare la percentuale di chiamate di rango da usare per l'esplorazione

* **Frequenza di aggiornamento del modello**: con quale frequenza viene ripetuto il training del modello.

* **Conservazione dei dati**: numero di giorni di dati da archiviare. Questo può influisca sulle valutazioni offline, che vengono usate per migliorare il ciclo di apprendimento.

## <a name="use-rank-and-reward-apis"></a>Usare le API Rank e Reward

* **Rank**: date le azioni con funzionalità e le funzionalità del contesto, usare Esplora o exploit per restituire l'azione principale (elemento di contenuto).

    * **Azioni**: le azioni sono elementi di contenuto, ad esempio prodotti o promozioni, tra cui scegliere. Il Personalizzatore sceglie l'azione principale (ID azione ricompensa restituito) da mostrare agli utenti tramite l'API Rank.

    * **Contesto**: per fornire un rango più preciso, fornire informazioni sul contesto, ad esempio:
        * L'utente.
        * Il dispositivo che usa.
        * Ora corrente.
        * Altri dati sulla situazione corrente.
        * Dati cronologici sull'utente o sul contesto.

        Le specifiche applicazioni possono avere informazioni diverse sul contesto.

    * **[Funzionalità](concepts-features.md)**: unità di informazioni relative a un elemento di contenuto o a un contesto utente. Assicurarsi di usare solo le funzionalità aggregate. Non usare orari specifici, ID utente o altri dati non aggregati come funzionalità.

        * Una _funzionalità azione_ è rappresentata dai metadati sul contenuto.
        * Una _funzionalità di contesto_ è costituita dai metadati sul contesto in cui viene presentato il contenuto.

* **Esplorazione**: il servizio di personalizzazione sta esplorando quando, anziché restituire l'azione migliore, sceglie un'azione diversa per l'utente. Il servizio Personalizza esperienze evita scenari di deriva e di stallo e può adattarsi al comportamento in corso dell'utente tramite esplorazione.

* **Exploit**: il servizio di personalizzazione usa il modello corrente per decidere l'azione migliore in base ai dati passati.

* **Durata dell'esperimento**: periodo di tempo durante il quale il servizio di personalizzazione resta in attesa di una ricompensa, a partire dal momento in cui si è verificata la chiamata di rango per quell'evento.

* **Eventi inattivi**: un evento inattivo è quello in cui è stato chiamato Rank, ma non si è certi che l'utente visualizzerà mai il risultato, a causa delle decisioni relative alle applicazioni client. Gli eventi inattivi consentono di creare e archiviare i risultati della personalizzazione e quindi di decidere di rimuoverli in seguito senza influire sul modello di Machine Learning.


* **Ricompensa**: una misura di come l'utente ha risposto all'ID dell'azione di ricompensa restituito dall'API di rango, come un punteggio compreso tra 0 e 1. Il valore da 0 a 1 viene impostato dalla logica di business in base all'efficacia della scelta per realizzare gli obiettivi aziendali della personalizzazione. Il ciclo di apprendimento non archivia questo premio come cronologia utente individuale.

## <a name="evaluations"></a>Giudizi

### <a name="offline-evaluations"></a>Valutazioni offline

* **Valutazione**: una valutazione offline determina i criteri di apprendimento migliori per il ciclo in base ai dati dell'applicazione.

* **Criteri di apprendimento**: il modo in cui la personalizzazione del training di un modello su ogni evento verrà determinato da alcuni parametri che influiscono sul funzionamento dell'algoritmo di machine learning. Un nuovo ciclo di apprendimento inizia con un **criterio di apprendimento**predefinito, che può produrre prestazioni moderate. Quando si eseguono [valutazioni](concepts-offline-evaluation.md), il Personalizzatore crea nuovi criteri di apprendimento specificamente ottimizzati per i casi d'uso del ciclo. Il Personalizzatore offre prestazioni significativamente migliori con i criteri ottimizzati per ogni ciclo specifico, generato durante la valutazione. Il criterio di apprendimento è denominato _impostazioni di apprendimento_ sul **modello e le impostazioni di apprendimento** per la risorsa di personalizzazione nella portale di Azure.

### <a name="apprentice-mode-evaluations"></a>Valutazioni della modalità Apprentice

La modalità apprendista fornisce le **metriche di valutazione**seguenti:
* **Baseline-ricompensa media**: vantaggi medi dell'impostazione predefinita dell'applicazione (baseline).
* **Personalizzatore-ricompensa media**: è stata potenzialmente raggiunta la media del personalizzatore dei premi totali.
* **Ricompensa in sequenza media**: rapporto tra la linea di base e la ricompensa del personalizzatore, normalizzato negli eventi 1000 più recenti.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [etica e sull'uso responsabile](ethics-responsible-use.md)