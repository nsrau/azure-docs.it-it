---
title: Reliable Collections | Documentazione Microsoft
description: "I servizi con stato di Service Fabric forniscono raccolte Reliable Collections che consentono di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/18/2016
ms.author: mcoskun
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9d8be5bed137ae00dec4f66fc821a6415f269ea1


---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduzione alle Reliable Collections nei servizi con stato di Service Fabric
Reliable Collections consente di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza nello stesso modo in cui si sviluppano applicazioni per singoli computer. Le classi presenti nello spazio dei nomi **Microsoft.ServiceFabric.Data.Collections** forniscono un set di raccolte predefinite che assicurano automaticamente la disponibilità elevata dello stato. Gli sviluppatori devono solo programmare le API Reliable Collections e consentire a queste raccolte di gestire lo stato replicato e locale.

La differenza principale tra le raccolte Reliable Collections e altre tecnologie a disponibilità elevata, ad esempio Redis e i servizi tabelle e code di Azure, consiste nel fatto che lo stato viene mantenuto in locale nell'istanza del servizio e ne viene assicurata al tempo stesso la disponibilità elevata. Ciò significa che:

* Tutte le operazioni di lettura sono locali, assicurando quindi bassa latenza e velocità effettiva elevata.
* Tutte le operazioni di scrittura eseguono il numero minimo di operazioni di IO di rete, assicurando quindi bassa latenza e velocità effettiva elevata.

![Immagine dell'evoluzione delle raccolte.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Le raccolte Reliable Collections possono essere considerate l'evoluzione naturale delle classi **System.Collections** , ovvero un nuovo set di raccolte progettate per le applicazioni per il cloud e più computer che non aumentano le complessità per gli sviluppatori. Come tali, le raccolte Reliable Collections sono:

* Replicate: le modifiche apportate allo stato vengono replicate per assicurare disponibilità elevata.
* Persistenti: i dati vengono resi persistenti sul disco per assicurarne la durabilità in caso di guasti su larga scala, ad esempio l'interruzione dell'alimentazione in un data center.
* Asincrone: le API sono asincrone per assicurare che i thread non vengano bloccati durante le operazioni di IO.
* Transazionali: le API utilizzano l'astrazione delle transazioni per consentire all'utente di gestire facilmente più raccolte Reliable Collections all'interno di un servizio.

Le raccolte Reliable Collections offrono garanzie predefinite di coerenza assoluta per facilitare la definizione della logica relativa allo stato delle applicazioni.
La coerenza assoluta è ottenuta assicurando che i commit delle transazioni siano completati solo dopo che l'intera transazione è stata registrata su un quorum di repliche di maggioranza, inclusa quella primaria.
Per ottenere una coerenza più debole, le applicazioni possono rinviare un acknowledgement al client/richiedente prima della restituzione del commit asincrono.

Le API Reliable Collections sono un'evoluzione delle API delle raccolte disponibili nello spazio dei nomi **System.Collections.Concurrent** :

* Asincrone: restituiscono un'attività dal momento che, a differenza delle raccolte Concurrent Collections, le operazioni vengono replicate e rese persistenti.
* Senza parametri out: usano `ConditionalValue<T>` per restituire una variabile booleana e un valore anziché parametri out. `ConditionalValue<T>` è come `Nullable<T>` ma non richiede una T per essere una struttura.
* Transazioni: usano un oggetto transazione per consentire all'utente di raggruppare azioni su più raccolte Reliable Collections in una transazione.

Attualmente **Microsoft.ServiceFabric.Data.Collections** include due raccolte:

* [ReliableDictionary](https://msdn.microsoft.com/library/azure/dn971511.aspx): rappresenta una raccolta replicata, transazionale e asincrona di coppie chiave/valore. Simile a **ConcurrentDictionary**, sia la chiave che il valore possono essere di qualsiasi tipo.
* [ReliableQueue](https://msdn.microsoft.com/library/azure/dn971527.aspx): rappresenta una coda FIFO (First-In First-Out) replicata, transazionale e asincrona. Simile a **ConcurrentQueue**, il valore può essere di qualsiasi tipo.

## <a name="isolation-levels"></a>Livelli di isolamento
Il livello di isolamento definisce il grado in cui la transazione deve essere isolata dalle modifiche apportate da altre transazioni.
Le raccolte Reliable Collections supportano due livelli di isolamento:

* **Repeatable Read**: specifica che le istruzioni non possono leggere dati modificati da altre transazioni di cui non è ancora stato eseguito il commit e che nessun'altra transazione può modificare i dati letti dalla transazione corrente, finché quest'ultima non viene completata. Per altre informazioni, vedere [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snapshot**: specifica che i dati letti da qualsiasi istruzione in una transazione rappresenteranno la versione coerente dal punto di vista transazionale dei dati esistenti al momento dell'avvio della transazione.
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
| Enumerazione\Conteggio |Snapshot |Snapshot |

> [!NOTE]
> Alcuni esempi comuni per le operazioni sulla singola entità sono `IReliableDictionary.TryGetValueAsync` e `IReliableQueue.TryPeekAsync`.
> 
> 

Gli oggetti ReliableDictionary e ReliableQueue supportano entrambi il criterio "Read Your Writes".
In altri termini, qualsiasi operazione di scrittura all'interno di una transazione sarà visibile a una lettura successiva appartenente alla stessa transazione.

## <a name="locking"></a>Blocco
Nelle raccolte Reliable Collections tutte le transazioni si articolano in due fasi: una transazione non rilascia i blocchi acquisiti fino a quando non termina con un'interruzione o un commit.

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

La matrice di compatibilità dei blocchi è disponibile di seguito:

| Richiesto\Concesso | Nessuno | Condiviso | Aggiornamento | Esclusivo |
| --- |:--- |:--- |:--- |:--- |
| Condiviso |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Aggiornamento |Nessun conflitto |Nessun conflitto |Conflitto |Conflitto |
| Esclusivo |Nessun conflitto |Conflitto |Conflitto |Conflitto |

Si noti che un argomento timeout delle API Reliable Collections viene usato per il rilevamento dei deadlock.
Si supponga, ad esempio, che due transazioni (T1 e T2) stiano cercando di leggere e aggiornare K1.
È possibile che si verifichi un deadlock, poiché entrambe le transazioni si concludono con il blocco condiviso.
In questo caso, potrebbe verificarsi il timeout di una o di entrambe le operazioni.

Lo scenario di deadlock sopra descritto è un perfetto esempio di come il blocco di aggiornamento possa impedire i deadlock.

## <a name="persistence-model"></a>Modello di persistenza
Reliable State Manager e le raccolte Reliable Collections seguono un modello di persistenza basato su log e checkpoint.
Si tratta di un modello in cui ogni modifica apportata allo stato viene registrata sul disco e applicata solo in memoria.
Lo stesso stato completo viene reso persistente solo occasionalmente (noto anche come Checkpoint).
Il vantaggio è che per migliorare le prestazioni, le differenze vengono trasformate in operazioni di scrittura sequenziali di solo accodamento sul disco.

Per comprendere meglio il modello basato su log e checkpoint, considerare prima di tutto lo scenario con disco infinito.
Reliable State Manager registra ogni operazione prima che venga replicata.
Questo consente alla raccolta Reliable Collections di applicare l'operazione solo in memoria.
Dal momento che i log sono persistenti, anche se la replica ha esito negativo e deve essere riavviata, Reliable State Manager dispone di informazioni sufficienti per riprodurre tutte le operazioni perse dalla replica.
Poiché il disco è infinito, non è mai necessario rimuovere i record del log e Reliable Collections deve gestire solo lo stato in memoria.

Si prenda ora in considerazione lo scenario con disco finito.
Mano a mano che si accumulano i record di log, Reliable State Manager non avrà più spazio su disco a disposizione.
Prima che questo accada, Reliable State Manager deve troncare il proprio log per fare spazio ai record più recenti.
Richiederà quindi alle Reliable Collections di inserire un checkpoint del proprio stato in memoria sul disco.
È responsabilità delle raccolte Reliable Collections rendere persistente il proprio stato fino a tale punto.
Dopo che le raccolte Reliable Collections hanno completato i propri checkpoint, Reliable State Manager può troncare il log per liberare spazio su disco.
In questo modo, quando è necessario riavviare la replica, le raccolte Reliable Collections ripristineranno il proprio stato in corrispondenza del checkpoint e Reliable State Manager ripristinerà e riprodurrà tutte le modifiche apportate allo stato a partire dal checkpoint.

> [!NOTE]
> L'aggiunta di un altro valore di checkpoint migliora le prestazioni del ripristino nella maggior parte dei casi.
> Questo avviene perché i checkpoint contengono solo le versioni più recenti.
> 
> 

## <a name="recommendations"></a>Indicazioni
* Non modificare un oggetto di tipo personalizzato restituito dalle operazioni di lettura, ad esempio `TryPeekAsync` o `TryGetValueAsync`. Le raccolte Reliable Collections, così come le raccolte Concurrent Collections, restituiscono un riferimento agli oggetti, non una copia.
* Eseguire una copia completa dell'oggetto di tipo personalizzato restituito prima di modificarlo. Poiché le strutture e i tipi predefiniti vengono passati per valore, non è necessario eseguirne una copia completa.
* Non usare `TimeSpan.MaxValue` per i timeout. I timeout devono essere usati per rilevare i deadlock.
* Non usare una transazione dopo che ne è stato eseguito il commit, è stata interrotta o eliminata.
* Non usare un'enumerazione all'esterno dell'ambito di transazione nella quale è stata creata.
* Non creare una transazione all'interno dell'istruzione `using` di un'altra transazione. Questa operazione può causare deadlock.
* Verificare che l'implementazione di `IComparable<TKey>` sia corretta. Il sistema presenta dipendenze sull'implementazione per l'unione dei checkpoint.
* Usare il blocco di aggiornamento durante la lettura di un elemento con l'intenzione di aggiornarlo in modo da evitare una determinata classe di deadlock.
* Per il ripristino di emergenza, è consigliabile usare la funzionalità di backup e ripristino.
* Evitare di combinare nella stessa transazione le operazioni a singola entità e a più entità, ad esempio `GetCountAsync`, `CreateEnumerableAsync`, a causa dei diversi livelli di isolamento.

Occorre tenere presente i concetti seguenti:

* Il timeout predefinito di tutte le API Reliable Collections è di 4 secondi. La maggior parte degli utenti non deve ignorare questo valore.
* Il token di annullamento predefinito è `CancellationToken.None` in tutte le API di Reliable Collections.
* Il parametro di tipo di chiave (*TKey*) per un oggetto ReliableDictionary deve implementare correttamente `GetHashCode()` e `Equals()`. Le chiavi non devono essere modificabili.
* Per ottenere una disponibilità elevata per le raccolte Reliable Collections, ogni servizio deve avere almeno un set di repliche di destinazione costituito da un minimo di 3 repliche.
* Le operazioni di lettura sul secondario possono leggere le versioni che non sono vincolate a un quorum.
  Ciò significa che una versione dei dati che viene letta da un singolo secondario potrebbe essere elaborata in modo non corretto.
  Naturalmente, le letture del primario sono sempre stabili: non possono verificarsi elaborazioni non corrette.

## <a name="next-steps"></a>Passaggi successivi
* [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
* [Eseguire il backup e il ripristino di Reliable Services (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager configuration (Configurazione di Reliable State Manager)](service-fabric-reliable-services-configuration.md)
* [Introduzione ai servizi API Web di Microsoft Azure Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)




<!--HONumber=Nov16_HO3-->


