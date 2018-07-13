---
title: Configurazione predefinita di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni sulla configurazione predefinita di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376161"
---
# <a name="default-values-and-boundaries"></a>Limiti e valori predefiniti

Questo documento descrive la configurazione predefinita di Conversation Learner e i principali limiti del servizio.

## <a name="default-configuration"></a>Configurazione predefinita

Parametro | Valore predefinito
--- | --- 
Timeout di sessione predefinito | 30 minuti

## <a name="boundaries"></a>Confini

Parametro | Limite
--- | --- 
API di creazione, numero massimo di chiamate HTTP al mese | 5M
API di creazione, numero massimo di chiamate HTTP al secondo | 25
API di sessione, numero massimo di chiamate HTTP al mese | 500K
API di sessione, numero massimo di chiamate HTTP al secondo | 10
Numero massimo di entità personalizzate (non a livello di codice) per applicazione | Vedere il documento [Limiti di LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries). Il numero effettivo nella pratica potrebbe essere leggermente inferiore.
Numero massimo di entità predefinite per applicazione | Vedere il documento [Limiti di LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Numero massimo di entità (in totale) per applicazione | 100
Numero massimo di azioni per applicazione | 32
Numero massimo di dialoghi di training per applicazione | 1000
Numero massimo di turni dell'utente per dialogo di training | 100
Numero massimo di dialoghi di log per applicazione | Nessun limite preimpostato, ma i dialoghi di log vengono mantenuti solo per un periodo fisso di tempo prima di essere rimossi.  Nell'interfaccia utente di Conversation Learner, inoltre, verranno visualizzati 100 dialoghi di log alla volta. 
Numero massimo di applicazioni per utente | Nessun limite preimpostato
Numero massimo di azioni senza attesa in sequenza | 5 (*)

(*) Dopo 5 azioni senza attesa in sequenza, tutte le azioni senza attesa verranno mascherate e Conversation Learner sceglierà tra le azioni con attesa disponibili.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione a Conversation Learner](./quickstart.md)
