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
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: e2af5cb193653736a0d75b4194e09d42282d2fa6
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203794"
---
# <a name="what-is-personalizer"></a>Informazioni su Personalizza esperienze

Personalizza esperienze di Azure è un servizio API basato sul cloud che consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale.

* È possibile fornire informazioni sui propri utenti e contenuti e ricevere l'azione principale da mostrare agli utenti. 
* Non è necessario pulire ed etichettare i dati prima di usare Personalizza esperienze.
* È possibile fornire il feedback a Personalizza esperienze al momento opportuno. 
* È possibile visualizzare l'analisi in tempo reale. 
* È possibile usare Personalizza esperienze come parte di un'operazione di data science più ampia per convalidare gli esperimenti esistenti.

## <a name="how-does-personalizer-work"></a>Come funziona Personalizza esperienze?

Personalizza esperienze usa modelli di Machine Learning per individuare l'azione di grado più alto in un contesto. L'applicazione client fornisce un elenco di possibili azioni, con informazioni su di esse, e informazioni sul contesto, che può includere informazioni sull'utente, sul dispositivo e così via. Personalizza esperienze determina l'azione da intraprendere. Una volta che l'applicazione client avrà usato l'azione scelta, fornirà il feedback a Personalizza esperienze sotto forma di punteggio. Dopo aver ricevuto il feedback, Personalizza esperienze aggiorna automaticamente il proprio modello usato per le classificazioni future. Con il tempo, Personalizza esperienze eseguirà il training di un modello che può suggerire l'azione migliore da scegliere in ogni contesto in base alle relative funzionalità.

## <a name="how-do-i-use-the-personalizer"></a>Come si usa Personalizza esperienze?

![Uso di Personalizza esperienze per scegliere quale video mostrare a un utente](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Scegliere un'esperienza nell'app da personalizzare.
1. Creare e configurare un'istanza del servizio di personalizzazione nel portale di Azure. Ogni istanza è un ciclo di Personalizza esperienze.
1. Usare l'SDK per chiamare Personalizza esperienze con le informazioni (_funzionalità_) sugli utenti e sul contenuto (_azioni_). Non è necessario fornire dati puliti ed etichettati prima di usare Personalizza esperienze. 
1. Nell'applicazione client, mostrare all'utente l'azione selezionata da Personalizza esperienze.
1. Usare l'SDK per inviare il feedback a Personalizza esperienze, indicando se l'utente ha selezionato l'azione di Personalizza esperienze. Si tratta di un _[punteggio di ricompensa](concept-rewards.md)_ .
1. Visualizzare l'analisi nel portale di Azure per valutare come funziona il sistema e in che modo i dati aiutano la personalizzazione.

## <a name="where-can-i-use-personalizer"></a>In quali situazioni è possibile usare Personalizza esperienze?

Ad esempio, l'applicazione client può aggiungere Personalizza esperienze per:

* Personalizzare l'articolo da evidenziare in un sito Web di notizie.    
* Ottimizzare il posizionamento degli annunci in un sito Web.
* Visualizzare un "articolo consigliato" personalizzato in un sito Web di acquisti.
* Suggerire elementi dell'interfaccia utente, come filtri da applicare a una specifica foto.
* Scegliere la risposta di un chatbot per chiarire le finalità dell'utente o suggerire un'azione.
* Classificare in ordine di priorità i suggerimenti dati a un utente riguardo il passaggio successivo in un processo aziendale.

Personalizza esperienze non è un servizio per rendere permanenti e gestire le informazioni del profilo utente o per registrare la cronologia o le preferenze dei singoli utenti. Personalizza esperienze apprende dalle funzionalità di ogni interazione nell'azione di un contesto in un singolo modello in grado di ottenere le massime ricompense quando vengono rilevate funzionalità simili. 

## <a name="personalization-for-developers"></a>Personalizzazione per sviluppatori

Il servizio Personalizza esperienze è caratterizzato da due API:

* Inviare informazioni (_funzionalità_) sugli utenti e sul contenuto (_azioni_) da personalizzare. Personalizza esperienze risponde con l'azione principale.
* Inviare il feedback a Personalizza esperienze sul funzionamento della classificazione come [punteggio di ricompensa](concept-rewards.md). 

![Sequenza di base di eventi per la personalizzazione](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni sulle [novità di Personalizza esperienze](whats-new.md)
* Vedere le informazioni sul [funzionamento di Personalizza esperienze](how-personalizer-works.md)
* Vedere le informazioni su [cos'è l'apprendimento per rinforzo](concepts-reinforcement-learning.md)
* [Informazioni sulle funzionalità e le azioni per la richiesta di classificazione](concepts-features.md)
* [Informazioni su come determinare il punteggio per la richiesta di ricompensa](concept-rewards.md)
* [Usare la demo interattiva](https://personalizationdemo.azurewebsites.net/)
