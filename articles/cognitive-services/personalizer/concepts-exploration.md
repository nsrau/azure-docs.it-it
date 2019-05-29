---
title: Esplorazione - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Con l'esplorazione, Personalizza esperienze può continuare a offrire buoni risultati, anche quando cambia il comportamento degli utenti. La scelta di un'impostazione di esplorazione è una decisione aziendale che riguarda la proporzione di interazioni degli utenti da usare per l'esplorazione al fine di migliorare il modello.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 0e11dd962f4cc1ff9b1938b309a1b405dbf20232
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606993"
---
# <a name="exploration-and-exploitation"></a>Esplorazione e sfruttamento

Con l'esplorazione, Personalizza esperienze può continuare a offrire buoni risultati, anche quando cambia il comportamento degli utenti.

Quando Personalizza esperienze riceve una chiamata a Classifica, restituisce un attributo RewardActionID che esegue una delle operazioni seguenti:
* Usa lo sfruttamento per individuare il comportamento utente più probabile in base al modello di Machine Learning corrente.
* Usa l'esplorazione, che non individua l'azione con la probabilità più elevata nella classifica.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizza esperienze usa attualmente un algoritmo denominato *epsilon greedy* per l'esplorazione. 

## <a name="choosing-an-exploration-setting"></a>Scelta di un'impostazione di esplorazione

Per configurare la percentuale di traffico da usare per l'esplorazione, nel portale di Azure è disponibile la pagina **Impostazioni** relativa a Personalizza esperienze. Questa impostazione determina la percentuale di chiamate a Classifica che eseguono l'esplorazione. 

Personalizza esperienze determina se eseguire l'esplorazione o lo sfruttamento usando questa probabilità per ogni chiamata a Classifica. Questo comportamento è diverso rispetto a quanto accade in un framework A/B che blocca una gestione su ID utente specifici.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Procedure consigliate per la scelta di un'impostazione di esplorazione

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

La scelta di un'impostazione di esplorazione è una decisione aziendale che riguarda la proporzione di interazioni degli utenti da usare per l'esplorazione al fine di migliorare il modello. 

Un'impostazione pari a zero annulla molti dei vantaggi offerti da Personalizza esperienze. Con questa impostazione, Personalizza esperienze non usa alcuna interazione degli utenti per individuare le interazioni migliori. Questo comporta una condizione di stallo, una deviazione e infine una riduzione delle prestazioni del modello.

Un'impostazione su un valore troppo elevato annulla i vantaggi derivanti dall'apprendimento del comportamento degli utenti. L'impostazione dell'esplorazione su 100% implica una sequenza casuale costante e qualsiasi informazione sul comportamento degli utenti non influenzerebbe il risultato.

È importante non modificare il comportamento dell'applicazione a seconda che Personalizza esperienze esegua l'esplorazione o lo sfruttamento. Ciò potrebbe causare distorsioni dell'apprendimento e infine una riduzione delle potenziali prestazioni.

## <a name="next-steps"></a>Passaggi successivi

[Apprendimento per rinforzo](concepts-reinforcement-learning.md) 