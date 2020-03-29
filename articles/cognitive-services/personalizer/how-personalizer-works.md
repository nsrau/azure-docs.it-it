---
title: Funzionamento di Personalizza esperienze - Personalizza esperienze
description: Il _ciclo_ Personalizer usa l'apprendimento automatico per creare il modello che stima l'azione principale per il contenuto. Il modello viene addestrato esclusivamente sui dati inviati con le chiamate Classifica e Ricompensa.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623758"
---
# <a name="how-personalizer-works"></a>Funzionamento di Personalizza esperienze

La risorsa Personalizer, il _ciclo di apprendimento_, utilizza l'apprendimento automatico per creare il modello che prevede l'azione principale per il contenuto. Il modello viene addestrato esclusivamente sui dati inviati con le chiamate **Classifica** e **Ricompensa.** Ogni ciclo è completamente indipendente l'uno dall'altro.

## <a name="rank-and-reward-apis-impact-the-model"></a>Le API Di grado e Ricompensa influiscono sul modello

È possibile inviare _azioni con funzionalità_ e funzionalità di _contesto_ all'API Rank. L'API **Classifica** decide di usare:

* _Exploit_: Il modello attuale per decidere l'azione migliore in base ai dati passati.
* _Esplora:_ selezionare un'azione diversa anziché l'azione principale. Configurare [questa percentuale](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) per la risorsa Personalizer nel portale di Azure.You configure this percentage for your Personalizer resource in the Azure portal.

È possibile determinare il punteggio premio e inviarlo all'API Reward. l'API **Ricompensa**:

* Raccoglie i dati per eseguire il training del modello registrando le caratteristiche e i punteggi di ricompensa di ogni chiamata a Classifica.
* Utilizza tali dati per aggiornare il modello in base alla configurazione specificata in _Criteri di apprendimento_.

## <a name="your-system-calling-personalizer"></a>Il tuo sistema chiama Personalizer

L'immagine seguente mostra il flusso architetturale delle chiamate a Classifica e Ricompensa:

![testo alternativo](./media/how-personalizer-works/personalization-how-it-works.png "Funzionamento della personalizzazione")

1. È possibile inviare _azioni con funzionalità_ e funzionalità di _contesto_ all'API Rank.

    * Il personalista decide se sfruttare il modello corrente o esplorare nuove scelte per il modello.
    * Il risultato della classificazione viene inviato all'hub eventi.
1. Il rango superiore viene restituito al sistema come _ID azione premio_.
    Il sistema presenta tale contenuto e determina un punteggio premio in base alle proprie regole aziendali.
1. Il sistema restituisce il punteggio di ricompensa al ciclo di apprendimento.
    * Quando Personalizza esperienze riceve la ricompensa, la invia all'hub eventi.
    * La classifica e la ricompensa sono correlate.
    * Il modello di intelligenza artificiale viene aggiornato in base ai risultati della correlazione.
    * Il motore di inferenza viene aggiornato con il nuovo modello.

## <a name="personalizer-retrains-your-model"></a>Personalizer rimodella il tuo modello

Il programma di aggiornamento della modalità di ricerca riequiligge il modello in base all'impostazione di **aggiornamento della frequenza** del modello nella risorsa Personalizer nel portale di Azure.Personalizer retrains your model based on your Model frequency update setting on your Personalizer resource in the Azure portal.

Il dispositivo di configurazione usa tutti i dati attualmente conservati, in base all'impostazione **Conservazione dei dati** in numero di giorni nella risorsa Personalizer nel portale di Azure.Personalizer uses all the data currently retained, based on the Data retention setting in number of days on your Personalizer resource in the Azure portal.

## <a name="research-behind-personalizer"></a>Ricerca alla base di Personalizza esperienze

Personalizza esperienze si basa su dati scientifici e ricerche nel campo dell'[apprendimento per rinforzo](concepts-reinforcement-learning.md), tra cui documenti, attività di ricerca e aree di studio in corso in Microsoft Research.

## <a name="next-steps"></a>Passaggi successivi

Scopri gli [scenari principali](where-can-you-use-personalizer.md) per Personalizer