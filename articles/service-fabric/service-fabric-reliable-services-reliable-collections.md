<properties
   pageTitle="Reliable Collections | Microsoft Azure"
   description="I servizi con stato dell’infrastruttura di servizi forniscono delle Reliable Collections che consentono di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/11/2015"
   ms.author="mcoskun"/>

# Introduzione alle Reliable Collections nei servizi con stato dell’infrastruttura di servizi

Le raccolte Reliable Collections consentono di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza nello stesso modo in cui vengono sviluppate applicazioni per singoli computer. Le classi presenti nello spazio dei nomi `Microsoft.ServiceFabric.Data.Collections` forniscono un insieme di raccolte predefinite che assicurano automaticamente la disponibilità elevata dello stato. Gli sviluppatori devono soltanto programmare le API delle raccolte Reliable Collections e consentire a queste ultime di gestire lo stato replicato e locale.

La differenza principale tra le raccolte Reliable Collections e altre tecnologie a disponibilità elevata, ad esempio Redis e i servizi tabelle e code di Azure, consiste nel fatto che lo stato viene mantenuto in locale nell'istanza del servizio e ne viene assicurata al tempo stesso la disponibilità elevata. Ciò significa che:

1. Tutte le operazioni di lettura sono locali, assicurando quindi bassa latenza e velocità effettiva elevata.
2. Tutte le operazioni di scrittura devono sostenere il numero minimo di operazioni di IO di rete, assicurando quindi bassa latenza e velocità effettiva elevata.

![Immagine dell'evoluzione delle raccolte.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Le raccolte Reliable Collections possono essere considerate l'evoluzione naturale delle classi `System.Collections`, ovvero un nuovo insieme di raccolte progettate per le applicazioni per il cloud e su più macchine che non aumentano la complessità per gli sviluppatori. In quanto tali, queste raccolte sono:

1. Replicate: le modifiche apportate allo stato vengono replicate per assicurare disponibilità elevata.
2. Persistenti: i dati vengono resi persistenti sul disco per assicurarne la durabilità in caso di guasti su larga scala, ad esempio l'interruzione dell'alimentazione in un data center.
3. Asincrone: le API sono asincrone per assicurare che i thread non vengano bloccati durante le operazioni di IO.
4. Transazionali: le API utilizzano l'astrazione delle transazioni per consentire all'utente di gestire facilmente più raccolte Reliable Collections all'interno di un servizio.

Le raccolte Reliable Collections offrono garanzie predefinite di coerenza assoluta per facilitare la definizione della logica relativa allo stato delle applicazioni. La coerenza assoluta viene ottenuta assicurando che i commit delle transazioni vengano completati solo dopo che l'intera transazione è stata applicata a un quorum di repliche, inclusa quella principale. Per ottenere una coerenza più debole, l'applicazione può rinviare un acknowledgement al client/richiedente prima della restituzione del commit asincrono.

Le API delle raccolte Reliable Collections sono un'evoluzione delle API delle raccolte Concurrent Collections (disponibili nello spazio dei nomi `System.Collections.Concurrent`) e sono:

1. Asincrone: restituiscono un'attività dal momento che, a differenza delle raccolte Concurrent Collections, le operazioni vengono replicate e rese persistenti.
2. Senza parametri out: usano `ConditionalResult<T>` per restituire una variabile booleana e un valore anziché parametri out. `ConditionalResult<T>` è simile a `Nullable<T>` ma non necessità di T per essere una struttura.
3. Transazioni: usano un oggetto transazione per consentire all'utente di raggruppare azioni su più raccolte Reliable Collections in una transazione.

Attualmente `Microsoft.ServiceFabric.Data.Collections` contiene due raccolte:

1. [ReliableDictionary](https://msdn.microsoft.com/library/azure/dn971511.aspx): rappresenta una raccolta replicata, transazionale e asincrona di coppie chiave/valore. Simile a `ConcurrentDictionary`, sia la chiave che il valore possono essere di qualsiasi tipo.
2. [ReliableQueue](https://msdn.microsoft.com/library/azure/dn971527.aspx): rappresenta una coda FIFO (First-In First-Out) replicata, transazionale e asincrona. Simile a `ConcurrentQueue`, il valore può essere di qualsiasi tipo.

## Livelli di isolamento
Il livello di isolamento è una misura del grado di isolamento raggiunto. Il termine isolamento indica che una transazione si comporta come se si trovasse in un sistema che consente a una sola transazione di essere in corso in un determinato momento.

Le raccolte Reliable Collections scelgono automaticamente il livello di isolamento da usare per una determinata operazione di lettura a seconda dell'operazione stessa e del ruolo della replica.

Le raccolte Reliable Collections supportano due livelli di isolamento:

- **Repeatable Read**: "Specifica che le istruzioni non possono leggere dati modificati da altre transazioni di cui non è ancora stato eseguito il commit e che nessun'altra transazione può modificare i dati letti dalla transazione corrente, fino al completamento della transazione corrente. (https://msdn.microsoft.com/it-IT/library/ms173763.aspx)".
- **Snapshot**: "Specifica che i dati letti da qualsiasi istruzione in una transazione rappresenteranno la versione consistente dal punto di vista transazionale dei dati esistenti al momento dell'avvio della transazione. La transazione può quindi accedere solo alle modifiche dei dati di cui è stato eseguito il commit prima dell'avvio della transazione. Le modifiche apportate da altre transazioni dopo l'inizio della transazione corrente non sono visibili per le istruzioni eseguite nella transazione corrente. Con questo livello di isolamento, è come se le istruzioni di una transazione operassero su uno snapshot dei dati di cui è stato eseguito il commit, corrispondente ai dati esistenti al momento dell'avvio della transazione. (https://msdn.microsoft.com/it-IT/library/ms173763.aspx)"

Gli oggetti ReliableDictionary e ReliableQueue supportano entrambi il criterio "Read Your Writes". In altri termini, qualsiasi operazione di scrittura all'interno di una transazione sarà visibile a una lettura successiva appartenente alla stessa transazione.

### ReliableDictionary
| Operazione\\Ruolo | Primario | Secondario |
| --------------------- | :--------------- | :--------------- |
| Lettura di entità singola | Repeatable Read | Snapshot |
| Enumerazione\\Conteggio | Snapshot | Snapshot |

### ReliableQueue
| Operazione\\Ruolo | Primario | Secondario |
| --------------------- | :--------------- | :--------------- |
| Lettura di entità singola | Snapshot | Snapshot |
| Enumerazione\\Conteggio | Snapshot | Snapshot |

## Modello di persistenza
Reliable State Manager e le raccolte Reliable Collections seguono un modello di persistenza basato su log e checkpoint. Si tratta di un modello in cui ciascuna modifica apportata allo stato viene registrata sul disco e applicata soltanto in memoria. Lo stato completo stesso viene reso persistente solo occasionalmente (noto anche come Checkpoint). Questo modello offre il vantaggio seguente:

- Per migliorare le prestazioni, le differenze vengono restituite in operazioni di scrittura sequenziali di solo accodamento sul disco.

Per comprendere meglio il modello basato su log e checkpoint, si prenda prima in considerazione lo scenario con disco infinito. Reliable State Manager registra ogni operazione prima che questa venga replicata. Questo consente alla raccolta Reliable Collections di applicare l'operazione soltanto in memoria. Dal momento che i log sono persistenti, anche se la replica ha esito negativo e deve essere riavviata, Reliable State Manager dispone di informazioni sufficienti per riprodurre tutte le operazioni perse dalla replica. Poiché il disco è infinito, non è mai necessario rimuovere i record del log e Reliable Collection deve soltanto gestire lo stato in memoria.

Si prenda ora in considerazione lo scenario con disco finito. A un certo punto Reliable State Manager non avrà più spazio su disco a disposizione. Prima che questo accada, Reliable State Manager deve troncare il proprio log per fare spazio ai record più recenti. Richiederà quindi alle Reliable Collections di inserire un checkpoint del proprio stato in memoria sul disco. È responsabilità delle raccolte Reliable Collections rendere persistente il proprio stato fino a tale punto. Dopo che le raccolte Reliable Collections hanno completato i propri checkpoint, Reliable State Manager può troncare il log per liberare spazio su disco. In questo modo, quando è necessario riavviare la replica, le raccolte Reliable Collections ripristineranno il proprio stato in corrispondenza del checkpoint e Reliable State Manager ripristinerà e riprodurrà tutte le modifiche apportate allo stato a partire dal checkpoint.

## Blocco
Nelle raccolte Reliable Collections tutte le transazioni si articolano in due fasi: una transazione non rilascia i blocchi acquisiti fino a quando non termina con un'interruzione o un commit.

Le raccolte Reliable Collections acquisiscono sempre blocchi esclusivi. Per le operazioni di lettura, il blocco dipende da due fattori. Le operazioni di lettura eseguite mediante Shapshot Isolation sono prive di blocchi. Per impostazione predefinita, ogni operazione di lettura ripetibile acquisisce blocchi condivisi. Tuttavia, per ogni operazione di lettura che supporta Repeatable Read, l'utente può chiedere un blocco di aggiornamento al posto del blocco condiviso. Il blocco di aggiornamento è asimmetrico e viene usato per impedire una forma comune di deadlock che si verifica quando più transazioni bloccano le risorse per un potenziale aggiornamento in un secondo momento.

La matrice di compatibilità dei blocchi è disponibile di seguito:

| Richiesto\\Concesso | Nessuno | Condiviso | Aggiornamento | Esclusivo |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Condiviso | Nessun conflitto | Nessun conflitto | Conflitto | Conflitto |
| Aggiornamento | Nessun conflitto | Nessun conflitto | Conflitto | Conflitto |
| Esclusivo | Nessun conflitto | Conflitto | Conflitto | Conflitto |

Si noti che l'argomento timeout delle API delle raccolte Reliable Collections viene usato per il rilevamento dei deadlock. Si supponga, ad esempio, che due transazioni (T1 e T2) stiano cercando di leggere e aggiornare K1. È possibile che si verifichi un deadlock, poiché entrambe le transazioni si concludono con il blocco condiviso. In questo caso, potrebbe verificarsi il timeout di una o di entrambe le operazioni.

Si noti che lo scenario di deadlock sopra descritto è un perfetto esempio di come il blocco di aggiornamento possa impedire i deadlock.

## Consigli

- **NON** modificare un oggetto di tipo personalizzato restituito da operazioni di lettura, ad esempio `TryPeekAsync` o `TryGetAsync`. Le raccolte Reliable Collections, così come le raccolte Concurrent Collections, restituiscono un riferimento agli oggetti, non una copia.
- **ESEGUIRE** una copia completa dell'oggetto di tipo personalizzato restituito prima di modificarlo. Poiché le strutture e i tipi incorporati vengono passati per valore, non è necessario eseguirne una copia completa.
- **NON** usare `TimeSpan.MaxValue` per i timeout. I timeout devono essere usati per rilevare i deadlock.
- **NON** creare una transazione all'interno dell'istruzione `using` di un'altra transazione. Questa operazione potrebbe causare dei deadlock.

Occorre tenere presente i concetti seguenti:

- Il timeout predefinito di tutte le API delle raccolte Reliable Collections è 4 secondi. La maggior parte degli utenti non deve ignorare questo valore.
- Il token di annullamento predefinito è `CancellationToken.None` in tutte le API di Reliable Collections.
- Il parametro di tipo di chiave (TKey) per un dizionario affidabile (ReliableDictionary) deve implementare correttamente GetHashCode e Equals (). Le chiavi non devono essere modificabili.
- All'interno di una raccolta le enumerazioni sono coerenti con gli snapshot. Le enumerazioni di più raccolte, tuttavia, non sono coerenti tra le diverse raccolte.
- Per ottenere una disponibilità elevata per le raccolte Reliable Collections, ogni servizio deve avere almeno un set di repliche di destinazione costituito da un minimo di 3 repliche.

## Passaggi successivi

- [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
- [Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)
- [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
- [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=Nov15_HO4-->