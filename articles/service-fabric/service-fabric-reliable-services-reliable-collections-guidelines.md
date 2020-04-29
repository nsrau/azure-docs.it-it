---
title: Linee guida per Reliable Collections
description: Linee guida e consigli per l'uso di Service Fabric Reliable Collections in un'applicazione Service Fabric di Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398532"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Linee guida e consigli per Reliable Collections in Azure Service Fabric
Questa sezione fornisce le linee guida per l'uso di Reliable State Manager e Reliable Collections. L'obiettivo è quello di aiutare gli utenti a evitare errori comuni.

Le linee guida sono organizzate come semplici consigli*su cosa fare* , *prendere in considerazione* , *evitare* e*non fare*.

* Non modificare un oggetto di tipo personalizzato restituito dalle operazioni di lettura, ad esempio `TryPeekAsync` o `TryGetValueAsync`. Le raccolte Reliable Collections, così come le raccolte Concurrent Collections, restituiscono un riferimento agli oggetti, non una copia.
* Eseguire una copia completa dell'oggetto di tipo personalizzato restituito prima di modificarlo. Poiché gli struct e i tipi predefiniti vengono passati per valore, non è necessario eseguirne una copia completa, a meno che non contengano campi di tipo Reference o proprietà che si intende modificare.
* Non usare `TimeSpan.MaxValue` per i timeout. I timeout devono essere usati per rilevare i deadlock.
* Non usare una transazione dopo che ne è stato eseguito il commit, è stata interrotta o eliminata.
* Non usare un'enumerazione all'esterno dell'ambito di transazione nella quale è stata creata.
* Non creare una transazione all'interno dell'istruzione di `using` un'altra transazione perché può causare deadlock.
* Non creare lo stato affidabile con `IReliableStateManager.GetOrAddAsync` e utilizzare lo stato affidabile nella stessa transazione. Viene restituito un valore InvalidOperationException.
* Verificare che l'implementazione di `IComparable<TKey>` sia corretta. Il sistema presenta dipendenze su `IComparable<TKey>` per l'unione di checkpoint e righe.
* Usare il blocco di aggiornamento durante la lettura di un elemento con l'intenzione di aggiornarlo in modo da evitare una determinata classe di deadlock.
* Si consiglia di mantenere un numero di raccolte Reliable Collections per partizione inferiore a 1000. Preferire le raccolte Reliable Collections con più elementi.
* Prendere in considerazione l'opportunità di mantenere gli elementi (ad esempio TKey + TValue per Reliable Dictionary) sotto gli 80 Kbyte: più sono piccoli, meglio è. In questo modo, è possibile ridurre l'uso di heap di oggetti di grandi dimensioni, oltre che i requisiti di dischi e I/O di rete. Spesso si riduce anche la replica dei dati duplicati quando viene aggiornata solo una piccola parte del valore. Un modo comune per ottenere questo in Reliable Dictionary è interrompere le righe in più righe.
* Prendere in considerazione l'uso della funzionalità di backup e ripristino per il ripristino di emergenza.
* Evitare di combinare nella stessa transazione le operazioni a singola entità e a più entità, ad esempio `GetCountAsync`, `CreateEnumerableAsync`, a causa dei diversi livelli di isolamento.
* Gestire InvalidOperationException. Le transazioni utente possono essere interrotte dal sistema per diversi motivi. Ad esempio, quando cambia il Gestore dello stato affidabile cambia il proprio ruolo da ruolo primario o quando una transazione a esecuzione prolungata blocca il troncamento del log delle transazioni. In questi casi, l'utente può ricevere un evento InvalidOperationException, che indica che la transazione è già stata terminata. Supponendo che l'interruzione della transazione non è stata richiesta dall'utente, il modo migliore per gestire questa eccezione è di eliminare la transazione, verificare se il token di annullamento è stato segnalato (o se il ruolo della replica è stato modificato) e in caso contrario creare una nuova transazione e riprovare.  

Occorre tenere presente i concetti seguenti:

* Il timeout predefinito di tutte le API Reliable Collections è di quattro secondi. La maggior parte degli utenti deve usare il timeout predefinito.
* Il token di annullamento predefinito è `CancellationToken.None` in tutte le API di Reliable Collections.
* Il parametro di tipo di chiave (*TKey*) per un oggetto ReliableDictionary deve implementare correttamente `GetHashCode()` e `Equals()`. Le chiavi non devono essere modificabili.
* Per ottenere una disponibilità elevata per le raccolte Reliable Collections, ogni servizio deve avere almeno un set di repliche di destinazione costituito da un minimo di 3 repliche.
* Le operazioni di lettura sul secondario possono leggere le versioni che non sono vincolate a un quorum.
  Ciò significa che una versione dei dati che viene letta da un singolo secondario potrebbe essere elaborata in modo non corretto.
  Le letture della replica primaria sono sempre stabili: non sono mai elaborate in modo non corretto.
* La sicurezza e la privacy dei dati resi persistenti tramite l'applicazione in una raccolta affidabile sono decisioni dell'utente e sono soggette a misure di protezione fornite dalla gestione dell'archiviazione, ad esempio la crittografia del disco del sistema operativo potrebbe essere usata per proteggere i dati inattivi.  

## <a name="volatile-reliable-collections"></a>Raccolte affidabili volatili
Quando si decide di usare le raccolte affidabili volatili, tenere presente quanto segue:

* ```ReliableDictionary```dispone del supporto volatile
* ```ReliableQueue```dispone del supporto volatile
* ```ReliableConcurrentQueue```non dispone del supporto volatile
* I servizi salvati in permanenza non possono essere resi volatili. La modifica ```HasPersistedState``` del flag ```false``` in richiede la ricreazione dell'intero servizio da zero
* Non è possibile rendere permanente i servizi volatili. La modifica ```HasPersistedState``` del flag ```true``` in richiede la ricreazione dell'intero servizio da zero
* ```HasPersistedState```è una configurazione a livello di servizio. Ciò significa che **tutte le** raccolte saranno rese permanente o volatili. Non è possibile combinare raccolte volatili e rese permanente
* La perdita di quorum di una partizione volatile comporta una perdita di dati completa
* Il backup e il ripristino non sono disponibili per i servizi volatili

## <a name="next-steps"></a>Passaggi successivi
* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transazioni e blocchi](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestione dei dati
  * [Backup e ripristino](service-fabric-reliable-services-backup-restore.md)
  * [Notifiche](service-fabric-reliable-services-notifications.md)
  * [Serializzazione e aggiornamento](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable State Manager configuration (Configurazione di Reliable State Manager)](service-fabric-reliable-services-configuration.md)
* Altro
  * [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
