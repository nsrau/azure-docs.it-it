---
title: File di inclusione
description: File di inclusione
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5d6d2285680884cf20daaeef0a1ade53afad5b90
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827688"
---
Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti.

Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

![Hub eventi](./media/event-hubs-partitions/partition.png)

Hub eventi mantiene i dati per un periodo di conservazione configurato che viene applicato a tutte le partizioni nell'hub eventi. Gli eventi scadono su base temporale; non è possibile eliminarli in modo esplicito. Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse.

![Hub eventi](./media/event-hubs-partitions/multiple_partitions.png)

Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 2 e 32. Il numero di partizioni non può essere modificato. È quindi consigliabile valutare le dimensioni a lungo termine in fase di impostazione del numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per impostare un numero di partizioni superiore a 32, contattare il team di Hub eventi.

Anche se le partizioni sono identificabili e consentono l'invio diretto, questa operazione non è consigliata per una partizione. In alternativa, è possibile utilizzare costrutti più generici introdotti nel [autori di eventi](../articles/event-hubs/event-hubs-features.md#event-publishers) sezione. 

Nelle partizioni viene inserita una sequenza di dati evento, che include il corpo dell'evento, un contenitore delle proprietà definito dall'utente e metadati quali l'offset nella partizione e il numero nella sequenza di flusso.

È consigliabile bilanciare unità elaborate di 1:1 e partizioni per ottenere una scalabilità ottimale. Una singola partizione ha un garantito in ingresso e in uscita di fino a un'unità di velocità effettiva. Benché sia possibile essere in grado di raggiungere una velocità effettiva in una partizione, le prestazioni non sono garantita. Ecco perché è fortemente consigliabile che il numero di partizioni in un hub eventi sia maggiore o uguale al numero di unità di velocità effettiva.

Data la velocità effettiva totale che si prevede di dover, si conosce il numero di unità di velocità effettiva desiderata e il numero minimo di partizioni, ma il numero di partizioni è opportuno avere? Selezionare numero di partizioni in base al parallelismo downstream che si desidera ottenere, nonché esigenze di velocità effettiva futura. Non sono previsti addebiti per il numero di partizioni all'interno di un Hub eventi.

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](../articles/event-hubs/event-hubs-availability-and-consistency.md).
