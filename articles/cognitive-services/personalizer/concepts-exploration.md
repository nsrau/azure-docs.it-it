---
title: Esplorazione - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Con l'esplorazione, Personalizza esperienze può continuare a offrire buoni risultati, anche quando cambia il comportamento degli utenti. La scelta di un'impostazione di esplorazione è una decisione aziendale che riguarda la proporzione di interazioni degli utenti da usare per l'esplorazione al fine di migliorare il modello.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490825"
---
# <a name="exploration-and-exploitation"></a>Esplorazione e sfruttamento

Con l'esplorazione, Personalizza esperienze può continuare a offrire buoni risultati, anche quando cambia il comportamento degli utenti.

Quando Personalizza esperienze riceve una chiamata a Classifica, restituisce un attributo RewardActionID che esegue una delle operazioni seguenti:
* Usa lo sfruttamento per individuare il comportamento utente più probabile in base al modello di Machine Learning corrente.
* Usa l'esplorazione, che non individua l'azione con la probabilità più elevata nella classifica.

Personalizza esperienze usa attualmente un algoritmo denominato *epsilon greedy* per l'esplorazione. 

## <a name="choosing-an-exploration-setting"></a>Scelta di un'impostazione di esplorazione

Configurare la percentuale di traffico da usare per l'esplorazione nella pagina di **configurazione** del portale di Azure per la personalizzazione. Questa impostazione determina la percentuale di chiamate a Classifica che eseguono l'esplorazione. 

Personalizza esperienze determina se eseguire l'esplorazione o lo sfruttamento usando questa probabilità per ogni chiamata a Classifica. Questo comportamento è diverso rispetto a quanto accade in un framework A/B che blocca una gestione su ID utente specifici.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Procedure consigliate per la scelta di un'impostazione di esplorazione

La scelta di un'impostazione di esplorazione è una decisione aziendale che riguarda la proporzione di interazioni degli utenti da usare per l'esplorazione al fine di migliorare il modello. 

Un'impostazione pari a zero annulla molti dei vantaggi offerti da Personalizza esperienze. Con questa impostazione, Personalizza esperienze non usa alcuna interazione degli utenti per individuare le interazioni migliori. Questo comporta una condizione di stallo, una deviazione e infine una riduzione delle prestazioni del modello.

Un'impostazione su un valore troppo elevato annulla i vantaggi derivanti dall'apprendimento del comportamento degli utenti. L'impostazione dell'esplorazione su 100% implica una sequenza casuale costante e qualsiasi informazione sul comportamento degli utenti non influenzerebbe il risultato.

È importante non modificare il comportamento dell'applicazione a seconda che Personalizza esperienze esegua l'esplorazione o lo sfruttamento. Ciò potrebbe causare distorsioni dell'apprendimento e infine una riduzione delle potenziali prestazioni.

## <a name="next-steps"></a>Passaggi successivi

[Apprendimento per rinforzo](concepts-reinforcement-learning.md) 