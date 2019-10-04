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
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481486"
---
Hub eventi fornisce lo streaming di messaggi tramite un modello consumer partizionato in cui ogni consumer legge solo un sottoinsieme specifico, o partizione, del flusso di messaggi. Questo modello consente la scalabilità orizzontale per l'elaborazione di eventi e fornisce altre funzionalità incentrate sul flusso non disponibili in code e argomenti.

Una partizione è una sequenza ordinata di eventi contenuta in un hub eventi. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

![Hub eventi](./media/event-hubs-partitions/partition.png)

Hub eventi mantiene i dati per un periodo di conservazione configurato che viene applicato a tutte le partizioni nell'hub eventi. Gli eventi scadono su base temporale; non è possibile eliminarli in modo esplicito. Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse.

![Hub eventi](./media/event-hubs-partitions/multiple-partitions.png)

Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 2 e 32. Il numero di partizioni non può essere modificato. È quindi consigliabile valutare le dimensioni a lungo termine in fase di impostazione del numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per impostare un numero di partizioni superiore a 32, contattare il team di Hub eventi.

È possibile impostarlo in modo che sia il valore massimo possibile, ovvero 32, al momento della creazione. Tenere presente che se si dispone di più di una partizione, gli eventi vengono inviati a più partizioni senza mantenere l'ordine, a meno che non si configurino i mittenti per inviare solo a una singola partizione fuori dalla 32 lasciando ridondanti le 31 partizioni rimanenti. Nel primo caso, sarà necessario leggere gli eventi in tutte le partizioni 32. Nel secondo caso, non vi sono costi aggiuntivi evidenti rispetto alla configurazione aggiuntiva che è necessario eseguire sull'host processore di eventi.

Anche se le partizioni sono identificabili e consentono l'invio diretto, questa operazione non è consigliata per una partizione. È invece possibile usare costrutti di livello superiore introdotti nella sezione [autori di eventi](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

Nelle partizioni viene inserita una sequenza di dati evento, che include il corpo dell'evento, un contenitore delle proprietà definito dall'utente e metadati quali l'offset nella partizione e il numero nella sequenza di flusso.

Si consiglia di bilanciare 1:1 unità elaborate e partizioni per ottenere una scalabilità ottimale. Una singola partizione ha un'entrata e uscita garantita di un massimo di un'unità di velocità effettiva. Sebbene sia possibile ottenere una velocità effettiva più elevata in una partizione, le prestazioni non sono garantite. Questo è il motivo per cui è consigliabile che il numero di partizioni in un hub eventi sia maggiore o uguale al numero di unità di velocità effettiva.

Data la velocità effettiva totale che si prevede di dover ottenere, si conosce il numero di unità di velocità effettiva necessarie e il numero minimo di partizioni, ma il numero di partizioni che è necessario avere? Scegliere il numero di partizioni in base al parallelismo downstream che si vuole ottenere, nonché le esigenze di velocità effettiva futura. Non è previsto alcun addebito per il numero di partizioni in un hub eventi.

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](../articles/event-hubs/event-hubs-availability-and-consistency.md).
