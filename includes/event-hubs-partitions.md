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
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481486"
---
Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti.

Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

![Hub eventi](./media/event-hubs-partitions/partition.png)

Hub eventi mantiene i dati per un periodo di conservazione configurato che viene applicato a tutte le partizioni nell'hub eventi. Gli eventi scadono su base temporale; non è possibile eliminarli in modo esplicito. Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse.

![Hub eventi](./media/event-hubs-partitions/multiple-partitions.png)

Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 2 e 32. Il numero di partizioni non può essere modificato. È quindi consigliabile valutare le dimensioni a lungo termine in fase di impostazione del numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per impostare un numero di partizioni superiore a 32, contattare il team di Hub eventi.

È possibile impostarlo come il valore più alto possibile, ovvero 32, al momento della creazione. Tenere presente che la presenza di più partizioni comporterà eventi inviati a più partizioni senza mantenere l'ordine, a meno che non si configuri i mittenti in modo che vengano inviati solo a una singola partizione su 32 lasciando ridondanti le restanti 31 partizioni. Nel primo caso, dovrai leggere gli eventi in tutte le 32 partizioni. In quest'ultimo caso, non vi è alcun costo aggiuntivo evidente a parte la configurazione aggiuntiva che è necessario effettuare su Event Processor Host.

Anche se le partizioni sono identificabili e consentono l'invio diretto, questa operazione non è consigliata per una partizione. È invece possibile utilizzare costrutti di livello superiore introdotti nella sezione [Editori](../articles/event-hubs/event-hubs-features.md#event-publishers) di eventi. 

Nelle partizioni viene inserita una sequenza di dati evento, che include il corpo dell'evento, un contenitore delle proprietà definito dall'utente e metadati quali l'offset nella partizione e il numero nella sequenza di flusso.

È consigliabile bilanciare le unità di velocità effettiva e le partizioni 1:1 per ottenere una scalabilità ottimale. Una singola partizione ha un ingresso e un'uscita garantiti fino a un'unità di velocità effettiva. Sebbene sia possibile ottenere una velocità effettiva più elevata in una partizione, le prestazioni non sono garantite. Per questo motivo è consigliabile che il numero di partizioni in un hub eventi sia maggiore o uguale al numero di unità di velocità effettiva.

Data la velocità effettiva totale di cui si intende necessità, si conosce il numero di unità di velocità effettiva e il numero minimo di partizioni, ma quante partizioni è necessario avere? Scegliere il numero di partizioni in base al parallelismo a valle che si desidera ottenere e alle esigenze future di velocità effettiva. Non è previsto alcun costo per il numero di partizioni presenti all'interno di un Hub eventi.

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](../articles/event-hubs/event-hubs-availability-and-consistency.md).
