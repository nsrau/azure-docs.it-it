---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cbd2d3b46ec339328e0d0889a722d39070104953
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347234"
---
Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti.

Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

![Diagramma che visualizza la sequenza di eventi dai meno recenti ai più recenti.](./media/event-hubs-partitions/partition.png)

Hub eventi mantiene i dati per un periodo di conservazione configurato che viene applicato a tutte le partizioni nell'hub eventi. Gli eventi scadono su base temporale; non è possibile eliminarli in modo esplicito. Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse.

![Hub eventi](./media/event-hubs-partitions/multiple-partitions.png)

Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 2 e 32. Il numero di partizioni non può essere modificato. È quindi consigliabile valutare le dimensioni a lungo termine in fase di impostazione del numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per impostare un numero di partizioni superiore a 32, contattare il team di Hub eventi.

È possibile impostarlo in modo che sia il valore massimo possibile, ovvero 32, al momento della creazione. Tenere presente che se si dispone di più di una partizione, gli eventi vengono inviati a più partizioni senza mantenerne l'ordine, a meno che non si configurino i mittenti per inviare solo a una singola partizione tra le 32, lasciando ridondanti le 31 partizioni rimanenti. Nel primo caso sarà necessario leggere gli eventi in tutte le 32 partizioni. Nel secondo caso non esiste alcun costo aggiuntivo evidente rispetto alla configurazione che è necessario eseguire in più sull'host processore di eventi.

Anche se le partizioni sono identificabili e consentono l'invio diretto, questa operazione non è consigliata per una partizione. È invece possibile usare i costrutti più generici introdotti nella sezione [Autori di eventi](../articles/event-hubs/event-hubs-features.md#event-publishers). 

Nelle partizioni viene inserita una sequenza di dati evento, che include il corpo dell'evento, un contenitore delle proprietà definito dall'utente e metadati quali l'offset nella partizione e il numero nella sequenza di flusso.

Per una scalabilità ottimale, è consigliabile bilanciare unità elaborate e partizioni. Una singola partizione prevede un massimo garantito di una unità elaborata in ingresso e in uscita. Anche se è possibile ottenere una velocità effettiva maggiore in una partizione, le prestazioni non sono garantite. È per questo motivo che il numero di partizioni in un hub eventi deve essere maggiore o uguale al numero di unità elaborate.

Considerata la velocità effettiva che si prevede sia necessaria, si conosce il numero di unità elaborate richieste e il numero minimo di partizioni. Ma quante partizioni sono necessarie? Scegliere il numero di partizioni in base al parallelismo downstream che si vuole ottenere, nonché alle esigenze di velocità effettiva future. Non è previsto alcun addebito per il numero di partizioni presenti in un hub eventi.

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](../articles/event-hubs/event-hubs-availability-and-consistency.md).
