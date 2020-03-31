---
title: Transazioni e modalità di blocco in raccolte affidabiliTransactions and Lock Modes in Reliable Collections
description: Transazioni e blocco delle raccolte Reliable Collections e di Reliable State Manager in Azure Service Fabric.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938918"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transazioni e modalità di blocco delle raccolte Reliable Collections in Azure Service Fabric

## <a name="transaction"></a>Transazione

Una transazione è una sequenza di operazioni eseguite in un'unica unità logica di lavoro. Presenta le proprietà [ACID](https://en.wikipedia.org/wiki/ACID) comuni (*atomicità*, *coerenza*, *isolamento,* *durabilità*) delle transazioni di database:

* **Atomicità**: una transazione deve essere un'unità di lavoro atomica. In altre parole, devono essere eseguite tutte le modifiche dei dati oppure non ne deve essere eseguita nessuna.
* **Coerenza**: al termine della transazione, tutti i dati devono essere coerenti e tutte le strutture di dati interne devono risultare corrette.
* **Isolamento**: le modifiche eseguite da transazioni simultanee devono essere isolate da quelle eseguite da qualsiasi altra transazione simultanea. Il livello di isolamento utilizzato per un'operazione all'interno di un [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) è determinato dal [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) che esegue l'operazione.
* **Durabilità**: gli effetti di una transazione completata sono permanenti all'interno del sistema. Le modifiche eseguite rimangono valide anche in caso di errore del sistema.

### <a name="isolation-levels"></a>Livelli di isolamento

Il livello di isolamento definisce il grado in cui la transazione deve essere isolata dalle modifiche apportate da altre transazioni.
Le raccolte Reliable Collections supportano due livelli di isolamento:

* **Repeatable Read**: specifica che le istruzioni non possono leggere dati modificati da altre transazioni di cui non è ancora stato eseguito il commit e che nessun'altra transazione può modificare i dati letti dalla transazione corrente, finché quest'ultima non viene completata.
* **Snapshot**: specifica che i dati letti da qualsiasi istruzione in una transazione rappresentano la versione coerente dal punto di vista transazionale dei dati esistenti al momento dell'avvio della transazione.
  La transazione può quindi riconoscere solo le modifiche dei dati di cui è stato eseguito il commit prima dell'avvio della transazione.
  Le modifiche ai dati apportate da altre transazioni dopo l'avvio della transazione corrente non sono visibili per le istruzioni eseguite nella transazione corrente.
  È come se le istruzioni di una transazione ottenessero uno snapshot dei dati di cui è stato eseguito il commit così come si presentavano al momento dell'avvio della transazione.
  Gli snapshot tra le Reliable Collections sono coerenti.

Le raccolte Reliable Collections scelgono automaticamente il livello di isolamento da usare per una determinata operazione di lettura a seconda dell'operazione stessa e del ruolo della replica al momento della creazione della transazione.
La tabella seguente descrive i valori predefiniti del livello di isolamento per le operazioni Reliable Dictionary e Reliable Queue.

| Operazione\Ruolo | Primaria | Secondari |
| --- |:--- |:--- |
| Lettura di entità singola |Repeatable Read |Snapshot |
| Enumerazione, conteggio |Snapshot |Snapshot |

> [!NOTE]
> Alcuni esempi comuni per le operazioni sulla singola entità sono `IReliableDictionary.TryGetValueAsync` e `IReliableQueue.TryPeekAsync`.
> 

Sia il dizionario Reliable che il supporto Reliable Queue supportano *Read Your Writes*.
In altri termini, qualsiasi operazione di scrittura all'interno di una transazione sarà visibile a una lettura successiva appartenente alla stessa transazione.

## <a name="locks"></a>Locks

Nelle raccolte Reliable Collections tutte le transazioni implementano un rigoroso blocco in due fasi: una transazione non rilascia i blocchi acquisiti fino a quando non termina con un'interruzione o un commit.

Reliable Dictionary usa il blocco a livello di riga per tutte le operazioni di entità singola.
L'operazione Reliable Queue bilancia la concorrenza in cambio di una proprietà FIFO transazionale rigorosa.
Reliable Queue utilizza blocchi a livello `TryPeekAsync` di operazione `TryDequeueAsync` che consentono `EnqueueAsync` una transazione con and/or e una transazione con alla volta.
Si noti che per mantenere il modello FIFO, se `TryPeekAsync` o `TryDequeueAsync` rileva che la coda affidabile è vuota, bloccherà anche `EnqueueAsync`.

Le operazioni di scrittura acquisiscono sempre blocchi esclusivi.
Per le operazioni di lettura, il blocco dipende da un paio di fattori:For read operations, the locking depends on a couple of factors:

- Qualsiasi operazione di lettura eseguita utilizzando l'isolamento dello snapshot è senza blocchi.
- Per impostazione predefinita, ogni operazione di lettura ripetibile acquisisce blocchi condivisi.
- Tuttavia, per ogni operazione di lettura che supporta Repeatable Read, l'utente può chiedere un blocco di aggiornamento al posto del blocco condiviso.
Il blocco di aggiornamento è asimmetrico e viene usato per impedire una forma comune di deadlock che si verifica quando più transazioni bloccano le risorse per un potenziale aggiornamento in un secondo momento.

La matrice di compatibilità dei blocchi è disponibile nella tabella seguente:

| Richiesto\Concesso | nessuno | Condiviso | Aggiornamento | Esclusivo |
| --- |:--- |:--- |:--- |:--- |
| Condiviso |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Aggiornamento |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Esclusivo |Nessun conflitto |Conflitto |Conflitto |Conflitto |

L'argomento timeout nelle API Reliable Collections viene utilizzato per il rilevamento dei deadlock.
Si supponga, ad esempio, che due transazioni (T1 e T2) stiano cercando di leggere e aggiornare K1.
È possibile che si verifichi un deadlock, poiché entrambe le transazioni si concludono con il blocco condiviso.
In questo caso, si stimerà di una o entrambe le operazioni. I questo scenario, un blocco di aggiornamento potrebbe impedire un deadlock di questo tipo.

## <a name="next-steps"></a>Passaggi successivi

* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
* [Eseguire il backup e il ripristino di Reliable Services (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager configuration (Configurazione di Reliable State Manager)](service-fabric-reliable-services-configuration.md)
* [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
