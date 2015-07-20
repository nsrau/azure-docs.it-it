<properties
   pageTitle="Aggiornamento di un'applicazione di Service Fabric: serializzazione dei dati"
   description="Procedure consigliate per la serializzazione dei dati per garantire aggiornamenti corretti delle applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="jesseb"/>


# Aggiornamento di un'applicazione di Service Fabric: serializzazione dei dati

In un [aggiornamento in sequenza di un'applicazione](service-fabric-application-upgrade.md) l'aggiornamento viene applicato a un subset di nodi, procedendo con un dominio di aggiornamento per volta. Durante questo processo, alcuni domini di aggiornamento si troveranno con la versione dell'applicazione più recente e altri con quella meno recente. In questa fase la versione dell'applicazione più recente deve essere in grado di leggere la versione dei dati meno recente e viceversa. Se il formato dei dati non è compatibile con le versioni successive e precedenti, è possibile che l'aggiornamento abbia esito negativo o che si verifichi una perdita di dati. Questo articolo illustra come è costituito il formato dei dati e le procedure consigliate per garantire che i dati siano compatibili con le versioni successive e precedenti.


## Come è costituito il formato dei dati

In Service Fabric i dati persistenti e replicati provengono dalle classi C#. Questo vale per le applicazioni che usano [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), ovvero gli oggetti nei dizionari e nelle code affidabili, nonché per le applicazioni che usano [Stateful Reliable Actors](service-fabric-reliable-actors-introduction.md), ovvero lo stato sottostante dell'attore. Queste classi C# devono essere serializzabili per essere persistenti e replicate. Il formato dei dati pertanto viene definito dai campi e dalle proprietà che vengono serializzati, nonché dal modo in cui vengono serializzati. In `IReliableDictionary<int, MyClass>` ad esempio i dati sono costituiti da elementi `int` e `MyClass` serializzati.

### Modifiche del formato dei dati

Poiché il formato dei dati è determinato da classi C#, le modifiche delle classi possono comportare una modifica del formato dei dati. Procedere con cautela per assicurarsi che un aggiornamento in sequenza possa gestire la modifica dei formato dei dati. Esempi di operazioni che possono comportare modifiche del formato dei dati:

- Aggiunta o rimozione di campi o proprietà
- Ridenominazione di campi o proprietà
- Modifica dei tipi di campi o proprietà
- Modifica dello spazio dei nomi o del nome di una classe

### Serializzatore predefinito

Il serializzatore in genere è responsabile della lettura dei dati e della deserializzazione dei dati nella versione corrente, anche se sono in una versione precedente o *più recente*. Il serializzatore predefinito è [DataContractSerializer](https://msdn.microsoft.com/library/ms733127.aspx), che include regole ben definite di controllo delle versioni. Reliable Collections consente l'override del serializzatore, mentre Reliable Actors attualmente non consente questa operazione. Il serializzatore di dati svolge un ruolo importante per l'abilitazione degli aggiornamenti in sequenza. DataContractSerializer è il serializzatore consigliato per le applicazioni di Service Fabric.


## Importanza del formato dei dati nell'aggiornamento in sequenza

Durante un aggiornamento in sequenza esistono due scenari principali in cui è possibile che il serializzatore rilevi una versione dei dati precedente o *più recente*:

1. Dopo l'aggiornamento e il riavvio di un nodo, il nuovo serializzatore caricherà sul disco i dati persistenti della versione precedente.
2. Durante l'aggiornamento in sequenza è possibile che il cluster contenga una combinazione di versioni del codice precedenti e più recenti. Poiché le repliche possono essere posizionate in domini di aggiornamento diversi, è possibile che il serializzatore rilevi sia la nuova versione dei dati che quella precedente. Lo stesso serializzatore può essere nelle due versioni.

> [AZURE.NOTE]Con "nuova versione" e "versione precedente" si intende la versione del codice in esecuzione. Con "nuovo serializzatore" si intende il codice del serializzatore in esecuzione nella nuova versione dell'applicazione. Con "nuovi dati" si intende la classe C# serializzata della nuova versione dell'applicazione.

Le due versioni di codice e formato dei dati devono essere entrambe compatibili con le versioni successive e precedenti. In caso contrario, è possibile che l'aggiornamento in sequenza abbia esito negativo o che si verifichi una perdita di dati. L'aggiornamento in sequenza può avere esito negativo perché è possibile che il codice o il serializzatore generi eccezioni o errori quando rileva l'altra versione. È possibile che si verifichi una perdita di dati se ad esempio è stata aggiunta una nuova proprietà ma il serializzatore precedente la ignora durante la deserializzazione.


## Contratto dati

Il contratto dati è la soluzione consigliata per garantire la compatibilità dei dati. Include regole ben definite di controllo delle versioni per l'aggiunta, la rimozione e la modifica di campi. Supporta inoltre l'eredità delle classi, la gestione dei campi sconosciuti e l'uso di hook nel processo di serializzazione e deserializzazione. Per altre informazioni, vedere [Utilizzo di contratti dati](https://msdn.microsoft.com/library/ms733127.aspx).


## Passaggi successivi

[Esercitazione sull'aggiornamento](service-fabric-application-upgrade-tutorial.md)

[Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md)

[Argomenti avanzati](service-fabric-application-upgrade-advanced.md)
 

<!---HONumber=July15_HO2-->