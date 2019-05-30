---
title: Informazioni su Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su Conversation Learner e sul relativo funzionamento.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f8bc7590f2d7a622b4b1ffb21bfeccef89691fd5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389506"
---
# <a name="what-is-conversation-learner"></a>Informazioni su Conversation Learner

Conversation Learner consente la creazione e il training di interfacce di conversazione in grado di apprendere da interazioni di esempio. 

A differenza degli approcci tradizionali, Conversation Learner considera il contesto end-to-end di un dialogo per migliorare le risposte e offrire esperienze utente più avanzate. Coprendo un'ampia gamma di casi d'uso orientati alle attività, Conversation Learner applica l'apprendimento automatico in background per rendere l'interazione più intuitiva e per ridurre le probabilità che bot e agenti intelligenti risultino frustranti per gli utenti o generino costi aggiuntivi a livello di servizio clienti.

Lo sviluppatore inizia immettendo i dialoghi prototipo che vuole imitare. Il modello apprende mentre i dialoghi vengono immessi. Quando il modello funziona correttamente, il bot può essere distribuito agli utenti finali. Conversation Learner registra le conversazioni con gli utenti, che possono così essere esaminate dallo sviluppatore. Se vengono individuati errori, lo sviluppatore può apportare una correzione mirata. Il modello verrà sottoposto di nuovo a training e sarà immediatamente disponibile per l'uso.

Questo approccio riduce la creazione manuale di codice per la logica di controllo dei dialoghi e consente a titolari d'azienda o esperti del dominio di contribuire a un'interfaccia di conversazione senza una conoscenza preliminare dell'apprendimento automatico. Sia che venga distribuito nell'ambito di un bot, uno Smart Device o un agente intelligente, Conversation Learner può eseguire rapidamente l'iterazione di nuovi comportamenti, funzionalità o competenze e migliorarne velocemente la qualità. 

Conversation Learner consente agli sviluppatori di incrementare la rapidità di introduzione sul mercato e realizzare dialoghi corretti in più canali di conversazione tramite Microsoft Bot Framework oppure in modo autonomo con un'infrastruttura personalizzata.

Riepilogo e caratteristiche principali:

- Conversation Learner consente di sfruttare l'intelligenza artificiale per creare bot orientati alle attività.

- Usa una rete neurale ricorrente end-to-end (LSTM) e apprende direttamente da esempi di conversazioni a più turni. 

- Consente a progettisti, sviluppatori, utenti aziendali e operatori di call center di creare e gestire bot. 

- Offre la possibilità di integrare regole business e buon senso nel codice.

- Durante le sessioni di training, il modello della rete neurale viene usato per assegnare punteggi al successivo set di azioni previste nella conversazione. Lo sviluppatore di bot può quindi selezionare l'azione corretta ed eseguire il training della rete per fornire la risposta appropriata.
 
- Al termine del training, lo sviluppatore può usare i dialoghi di log delle interazioni utente per apportare correzioni alle risposte del bot e ripetere il training del modello. 

- È possibile chiamare API specifiche del dominio e di terze parti per completare le attività.

