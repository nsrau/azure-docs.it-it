---
title: Progettazione di funzioni di Azure per l'input identico
description: Creazione di funzioni di Azure da idempotente
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226866"
---
# <a name="designing-azure-functions-for-identical-input"></a>Progettazione di funzioni di Azure per l'input identico

La realtà di un'architettura basata su eventi e basata su messaggi impone la necessità di accettare richieste identiche mantenendo al tempo stesso l'integrità dei dati e la stabilità del sistema.

Per illustrare, prendere in considerazione un pulsante di chiamata all'ascensore. Quando si preme il pulsante, viene accesa e viene inviato un ascensore al pavimento. Pochi istanti dopo, un altro utente si unirà alla lobby. Questa persona sorride e preme il pulsante illuminato una seconda volta. Sorridendo e ridacchiando a se stessi come si ricorda che il comando per chiamare un ascensore è idempotente.

Quando si preme un pulsante di chiamata dell'ascensore, il secondo, il terzo o il quarto orario non ha alcun impatto sul risultato finale. Quando si preme il pulsante, indipendentemente dal numero di volte, l'ascensore viene inviato al pavimento. I sistemi idempotente, ad esempio l'ascensore, comportano lo stesso risultato indipendentemente dal numero di comandi identici eseguiti.

Quando si tratta di creare applicazioni, considerare gli scenari seguenti:

- Cosa accade se l'applicazione di controllo dell'inventario tenta di eliminare lo stesso prodotto più di una volta?
- In che modo l'applicazione di risorse umane si comporta se è presente più di una richiesta di creazione di un record dipendente per la stessa persona?
- Dove si ottiene il denaro se l'app Banking riceve 100 richieste di effettuare lo stesso ritiro?

Esistono molti contesti in cui le richieste a una funzione possono ricevere comandi identici. Alcune situazioni includono:

- Criteri di ripetizione che inviano la stessa richiesta molte volte
- Comandi memorizzati nella cache riprodotti all'applicazione
- Errori dell'applicazione che inviano più richieste identiche

Per proteggere l'integrità dei dati e l'integrità del sistema, un'applicazione idempotente contiene la logica che può contenere i comportamenti seguenti:

- Verifica dell'esistenza dei dati prima di tentare di eseguire un'eliminazione
- Verifica per determinare se i dati esistono già prima del tentativo di eseguire un'azione di creazione
- Riconciliazione della logica che crea la coerenza finale nei dati
- Controlli della concorrenza
- Rilevamento della duplicazione
- Convalida dell'aggiornamento dei dati
- Logica di Guard per verificare i dati di input

Infine, idempotenza viene ottenuto garantendo che sia possibile una determinata azione e che venga eseguita una sola volta.
