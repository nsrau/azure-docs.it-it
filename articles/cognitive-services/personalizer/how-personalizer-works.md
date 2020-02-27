---
title: Funzionamento di Personalizza esperienze - Personalizza esperienze
description: Il _ciclo_ di personalizzazione USA Machine Learning per compilare il modello che consente di stimare l'azione principale per il contenuto. Il training del modello viene eseguito esclusivamente sui dati inviati con le chiamate di rango e di ricompensa.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623758"
---
# <a name="how-personalizer-works"></a>Funzionamento di Personalizza esperienze

La risorsa di personalizzazione, il _ciclo di apprendimento_, USA Machine Learning per creare il modello che consente di stimare l'azione principale per il contenuto. Il training del modello viene eseguito esclusivamente sui dati inviati con le chiamate di **rango** e di **ricompensa** . Ogni ciclo è completamente indipendente l'uno dall'altro.

## <a name="rank-and-reward-apis-impact-the-model"></a>Le API Rank e Reward hanno un effetto sul modello

Si inviano _azioni con funzionalità_ e _funzionalità di contesto_ all'API Rank. L'API **Classifica** decide di usare:

* _Exploit_: modello corrente per decidere l'azione migliore in base ai dati passati.
* _Esplora_: selezionare un'azione diversa anziché l'azione principale. [Questa percentuale viene configurata](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) per la risorsa di personalizzazione nel portale di Azure.

Si determina il Punteggio di ricompensa e si invia tale Punteggio all'API Reward. l'API **Ricompensa**:

* Raccoglie i dati per eseguire il training del modello registrando le caratteristiche e i punteggi di ricompensa di ogni chiamata a Classifica.
* USA i dati per aggiornare il modello in base alla configurazione specificata nei _criteri di apprendimento_.

## <a name="your-system-calling-personalizer"></a>Il sistema che chiama il personale

L'immagine seguente mostra il flusso architetturale delle chiamate a Classifica e Ricompensa:

![testo alternativo](./media/how-personalizer-works/personalization-how-it-works.png "Funzionamento della personalizzazione")

1. Si inviano _azioni con funzionalità_ e _funzionalità di contesto_ all'API Rank.

    * Il Personalizzatore decide se sfruttare il modello corrente o esplorare nuove scelte per il modello.
    * Il risultato della classificazione viene inviato all'hub eventi.
1. Il rango superiore viene restituito al sistema come _ID azione Reward_.
    Il sistema presenta il contenuto e determina un punteggio di ricompensa basato sulle regole business.
1. Il sistema restituisce il Punteggio di ricompensa al ciclo di apprendimento.
    * Quando Personalizza esperienze riceve la ricompensa, la invia all'hub eventi.
    * La classifica e la ricompensa sono correlate.
    * Il modello di intelligenza artificiale viene aggiornato in base ai risultati della correlazione.
    * Il motore di inferenza viene aggiornato con il nuovo modello.

## <a name="personalizer-retrains-your-model"></a>Il Personalizzatore ripete il training del modello

Il Personalizzatore ripete il training del modello in base all'impostazione di **aggiornamento della frequenza del modello** sulla risorsa di personalizzazione nel portale di Azure.

Il Personalizzatore utilizza tutti i dati attualmente conservati, in base all'impostazione di **conservazione dei dati** in numero di giorni nella risorsa di personalizzazione nel portale di Azure.

## <a name="research-behind-personalizer"></a>Ricerca alla base di Personalizza esperienze

Personalizza esperienze si basa su dati scientifici e ricerche nel campo dell'[apprendimento per rinforzo](concepts-reinforcement-learning.md), tra cui documenti, attività di ricerca e aree di studio in corso in Microsoft Research.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli [scenari principali](where-can-you-use-personalizer.md) per la personalizzazione