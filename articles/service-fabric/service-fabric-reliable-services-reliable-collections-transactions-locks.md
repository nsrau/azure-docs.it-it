---
title: "Transazioni e modalità di blocco delle raccolte Reliable Collections in Azure Service Fabric | Microsoft Docs"
description: Transazioni e blocco delle raccolte Reliable Collections e di Reliable State Manager in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 3452473f5b2f86d29e46339c997193bc6403736a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transazioni e modalità di blocco delle raccolte Reliable Collections in Azure Service Fabric

## <a name="transaction"></a>Transazione
Una transazione è una sequenza di operazioni eseguite in un'unica unità logica di lavoro
e deve includere le proprietà seguenti, dette ACID (atomicità, coerenza, isolamento e durabilità) (vedere https://technet.microsoft.com/it-it/library/ms190612):
* **Atomicità**: una transazione deve essere un'unità di lavoro atomica. In altre parole, devono essere eseguite tutte le modifiche dei dati oppure non ne deve essere eseguita nessuna.
* **Coerenza**: al termine della transazione, tutti i dati devono essere coerenti e tutte le strutture di dati interne devono risultare corrette.
* **Isolamento**: le modifiche eseguite da transazioni simultanee devono essere isolate da quelle eseguite da qualsiasi altra transazione simultanea. Il livello di isolamento usato per un'operazione all'interno di un oggetto ITransaction è determinato dall'interfaccia IReliableState che esegue l'operazione.
* **Durabilità**: gli effetti di una transazione completata sono permanenti all'interno del sistema. Le modifiche eseguite rimangono valide anche in caso di errore del sistema.

### <a name="isolation-levels"></a>Livelli di isolamento
Il livello di isolamento definisce il grado in cui la transazione deve essere isolata dalle modifiche apportate da altre transazioni.
Le raccolte Reliable Collections supportano due livelli di isolamento:

* **Repeatable Read**: specifica che le istruzioni non possono leggere dati modificati da altre transazioni di cui non è ancora stato eseguito il commit e che nessun'altra transazione può modificare i dati letti dalla transazione corrente, finché quest'ultima non viene completata. Per altre informazioni, vedere [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snapshot**: specifica che i dati letti da qualsiasi istruzione in una transazione rappresentano la versione coerente dal punto di vista transazionale dei dati esistenti al momento dell'avvio della transazione.
  La transazione può quindi riconoscere solo le modifiche dei dati di cui è stato eseguito il commit prima dell'avvio della transazione.
  Le modifiche apportate da altre transazioni dopo l'inizio della transazione corrente non sono visibili per le istruzioni eseguite nella transazione corrente.
  È come se le istruzioni di una transazione ottenessero uno snapshot dei dati di cui è stato eseguito il commit così come si presentavano al momento dell'avvio della transazione.
  Gli snapshot tra le Reliable Collections sono coerenti.
  Per altre informazioni, vedere [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Le raccolte Reliable Collections scelgono automaticamente il livello di isolamento da usare per una determinata operazione di lettura a seconda dell'operazione stessa e del ruolo della replica al momento della creazione della transazione.
La tabella seguente descrive i valori predefiniti del livello di isolamento per le operazioni Reliable Dictionary e Reliable Queue.

| Operazione\Ruolo | Primario | Secondario |
| --- |:--- |:--- |
| Lettura di entità singola |Repeatable Read |Snapshot |
| Enumerazione, conteggio |Snapshot |Snapshot |

> [!NOTE]
> Alcuni esempi comuni per le operazioni sulla singola entità sono `IReliableDictionary.TryGetValueAsync` e `IReliableQueue.TryPeekAsync`.
> 

Gli oggetti ReliableDictionary e ReliableQueue supportano entrambi il criterio "Read Your Writes".
In altri termini, qualsiasi operazione di scrittura all'interno di una transazione sarà visibile a una lettura successiva appartenente alla stessa transazione.

## <a name="locks"></a>Blocchi
Nelle raccolte Reliable Collections tutte le transazioni implementano un rigoroso blocco in due fasi: una transazione non rilascia i blocchi acquisiti fino a quando non termina con un'interruzione o un commit.

L'operazione Reliable Dictionary usa il blocco a livello di riga per tutte le operazioni sulla singola entità.
L'operazione Reliable Queue bilancia la concorrenza in cambio di una proprietà FIFO transazionale rigorosa.
Reliable Queue usa i blocchi a livello di operazione che consentono una transazione con `TryPeekAsync` e/o `TryDequeueAsync` e una transazione con `EnqueueAsync` per volta.
Si noti che per mantenere il modello FIFO, se `TryPeekAsync` o `TryDequeueAsync` rileva che la coda affidabile è vuota, bloccherà anche `EnqueueAsync`.

Le operazioni di scrittura acquisiscono sempre blocchi esclusivi.
Per le operazioni di lettura, il blocco dipende da due fattori.
Le operazioni di lettura eseguite con Shapshot Isolation sono prive di blocchi.
Per impostazione predefinita, ogni operazione di lettura ripetibile acquisisce blocchi condivisi.
Tuttavia, per ogni operazione di lettura che supporta Repeatable Read, l'utente può chiedere un blocco di aggiornamento al posto del blocco condiviso.
Il blocco di aggiornamento è asimmetrico e viene usato per impedire una forma comune di deadlock che si verifica quando più transazioni bloccano le risorse per un potenziale aggiornamento in un secondo momento.

La matrice di compatibilità dei blocchi è disponibile nella tabella seguente:

| Richiesto\Concesso | Nessuno | Condiviso | Aggiornamento | Esclusivo |
| --- |:--- |:--- |:--- |:--- |
| Condiviso |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Aggiornamento |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Esclusivo |Nessun conflitto |Conflitto |Conflitto |Conflitto |

Per il rilevamento dei deadlock viene usato un argomento timeout delle API Reliable Collections.
Si supponga, ad esempio, che due transazioni (T1 e T2) stiano cercando di leggere e aggiornare K1.
È possibile che si verifichi un deadlock, poiché entrambe le transazioni si concludono con il blocco condiviso.
In questo caso, potrebbe verificarsi il timeout di una o di entrambe le operazioni.

Questo scenario di deadlock è un perfetto esempio di come il blocco di aggiornamento possa impedire i deadlock.

## <a name="next-steps"></a>Passaggi successivi
* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
* [Eseguire il backup e il ripristino di Reliable Services (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager configuration (Configurazione di Reliable State Manager)](service-fabric-reliable-services-configuration.md)
* [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

