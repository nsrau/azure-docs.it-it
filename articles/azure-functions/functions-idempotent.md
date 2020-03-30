---
title: Progettazione di funzioni di Azure per input identicoDesigning Azure Functions for identical input
description: Creazione di funzioni di Azure come idempotentiBuilding Azure Functions to be idempotent
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226866"
---
# <a name="designing-azure-functions-for-identical-input"></a>Progettazione di funzioni di Azure per input identicoDesigning Azure Functions for identical input

La realtà dell'architettura basata su eventi e dei messaggi determina la necessità di accettare richieste identiche, preservando al contempo l'integrità dei dati e la stabilità del sistema.

Per illustrare, si consideri un pulsante di chiamata dell'ascensore. Quando si preme il pulsante, si accende e un ascensore viene inviato al piano. Pochi istanti dopo, qualcun altro si unisce a te nell'atrio. Questa persona ti sorride e preme il pulsante illuminato una seconda volta. Sorridi e ridacchiate a te stesso mentre ti viene ricordato che il comando di chiamare un ascensore è idempotente.

Premendo un pulsante di chiamata in ascensore una seconda, terza o quarta volta non ha alcuna influenza sul risultato finale. Quando si preme il pulsante, indipendentemente dal numero di volte, l'ascensore viene inviato al piano. I sistemi idempotenti, come l'ascensore, generano lo stesso risultato, indipendentemente dal numero di volte in cui vengono emessi comandi identici.

Quando si tratta di creare applicazioni, considerare gli scenari seguenti:When it comes to building applications, consider the following scenarios:

- Cosa succede se l'applicazione di controllo dell'inventario tenta di eliminare lo stesso prodotto più di una volta?
- Come si comporta l'applicazione per le risorse umane se è presente più di una richiesta per creare un record dipendente per la stessa persona?
- Dove vanno i soldi se la tua app bancaria riceve 100 richieste per effettuare lo stesso prelievo?

Esistono molti contesti in cui le richieste a una funzione possono ricevere comandi identici. Alcune situazioni includono:Some situations include:

- Criteri di ripetizione dei tentativi che inviano la stessa richiesta più volteRetry policies sending the same request times
- Comandi memorizzati nella cache riprodotti nell'applicazione
- Errori dell'applicazione durante l'invio di più richieste identiche

Per proteggere l'integrità dei dati e l'integrità del sistema, un'applicazione idempotente contiene la logica che può contenere i comportamenti seguenti:To protect data integrity and system integrity, an idempotent application contains logic that may contain the following behaviors:

- Verifica dell'esistenza dei dati prima di tentare di eseguire un'eliminazione
- Verifica dell'esistono già dati prima di tentare di eseguire un'azione di creazione
- Logica di riconciliazione che crea un'eventuale coerenza nei datiReconciling logic that creates eventual consistency in data
- Controlli della concorrenzaConcurrency controls
- Rilevamento della duplicazione
- Convalida dell'aggiornamento dei dati
- Logica di protezione per verificare i dati di input

In definitiva, l'idempotency viene ottenuta assicurando che una determinata azione sia possibile e venga eseguita una sola volta.
