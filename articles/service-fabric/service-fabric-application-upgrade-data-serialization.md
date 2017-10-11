---
title: 'Aggiornamento di un''applicazione: serializzazione dei dati | Microsoft Docs'
description: "Procedure consigliate per la serializzazione dei dati e descrizione della sua influenza sugli aggiornamenti dell’applicazione in sequenza."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 6aa3ac7842df4657fca7f6b4264e1c6fe52dc0c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Come la serializzazione dei dati influenzi l’aggiornamento di un’applicazione
In un [aggiornamento in sequenza di un'applicazione](service-fabric-application-upgrade.md)l'aggiornamento viene applicato a un subset di nodi, procedendo con un dominio di aggiornamento per volta. Durante questo processo, alcuni domini di aggiornamento si trovano con la versione dell'applicazione più recente e altri con quella meno recente. Nella fase di distribuzione, la versione dell'applicazione più recente deve essere in grado di leggere la versione dei dati meno recente e viceversa. Se il formato dei dati non è compatibile con le versioni successive e precedenti, è possibile che l'aggiornamento abbia esito negativo o, peggio ancora, che i dati vengano persi o danneggiati. Questo articolo illustra come è costituito il formato dei dati e riporta le procedure consigliate per garantire che i dati siano compatibili con le versioni successive e precedenti.

## <a name="what-makes-up-your-data-format"></a>Come è costituito il formato dei dati
In Service Fabric di Azure, i dati persistenti e replicati provengono dalle classi C#. Questo vale per le applicazioni che usano [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), i cui dati sono gli oggetti nei dizionari e nelle code affidabili, e per le applicazioni che usano [Reliable Actors](service-fabric-reliable-actors-introduction.md), ovvero lo stato sottostante dell'attore. Queste classi C# devono essere serializzabili per essere persistenti e replicate. Il formato dei dati pertanto viene definito dai campi e dalle proprietà che vengono serializzati, nonché dal modo in cui vengono serializzati. In `IReliableDictionary<int, MyClass>` ad esempio i dati sono costituiti da elementi `int` e `MyClass` serializzati.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Il codice modifica quel risultato trasformandolo in una modifica del formato dei dati
Poiché il formato dei dati è determinato da classi C#, le modifiche delle classi possono comportare una modifica del formato dei dati. Procedere con cautela per assicurarsi che un aggiornamento in sequenza possa gestire la modifica del formato dei dati. Esempi di operazioni che possono comportare modifiche del formato dei dati:

* Aggiunta o rimozione di campi o proprietà
* Ridenominazione di campi o proprietà
* Modifica dei tipi di campi o proprietà
* Modifica dello spazio dei nomi o del nome di una classe

### <a name="data-contract-as-the-default-serializer"></a>Il Contratto dati è il serializzatore predefinito
Il serializzatore in genere è responsabile della lettura dei dati e della deserializzazione dei dati nella versione corrente, anche se sono in una versione precedente o *più recente* . Il serializzatore predefinito è [DataContractSerializer](https://msdn.microsoft.com/library/ms733127.aspx), che include regole ben definite di controllo delle versioni. Reliable Collections consente l'override del serializzatore, mentre Reliable Actors attualmente non consente questa operazione. Il serializzatore di dati svolge un ruolo importante per l'abilitazione degli aggiornamenti in sequenza. Data Contract Serializer è il serializzatore consigliato per le applicazioni Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Importanza del formato dei dati nell'aggiornamento in sequenza
Durante un aggiornamento in sequenza esistono due scenari principali in cui è possibile che il serializzatore rilevi una versione dei dati precedente o *più recente* :

1. Dopo l'aggiornamento e il riavvio di un nodo, il nuovo serializzatore caricherà sul disco i dati persistenti della versione precedente.
2. Durante l'aggiornamento in sequenza, il cluster conterrà una combinazione di versioni del codice precedenti e più recenti. Poiché le repliche possono essere posizionate in domini di aggiornamento diversi, e poiché le repliche si inviano dati tra loro, è possibile che sia la versione nuova che quella precedente del serializzatore rilevi sia la nuova versione dei dati che quella precedente.

> [!NOTE]
> Con "nuova versione" e "versione precedente" si intende la versione del codice in esecuzione. Con "nuovo serializzatore" si intende il codice del serializzatore in esecuzione nella nuova versione dell'applicazione. Con "nuovi dati" si intende la classe C# serializzata della nuova versione dell'applicazione.
> 
> 

Le due versioni di codice e formato dei dati devono essere entrambe compatibili con le versioni successive e precedenti. In caso contrario, è possibile che l'aggiornamento in sequenza abbia esito negativo o che si verifichi una perdita di dati. L'aggiornamento in sequenza può avere esito negativo perché è possibile che il codice o il serializzatore generi eccezioni o errori quando rileva l'altra versione. È possibile che si verifichi una perdita di dati se ad esempio è stata aggiunta una nuova proprietà ma il serializzatore precedente la ignora durante la deserializzazione.

Il contratto dati è la soluzione consigliata per garantire la compatibilità dei dati. Include regole ben definite di controllo delle versioni per l'aggiunta, la rimozione e la modifica di campi. Supporta inoltre l'eredità delle classi, la gestione dei campi sconosciuti e l'uso di hook nel processo di serializzazione e deserializzazione. Per altre informazioni, vedere [Utilizzo di contratti dati](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l’aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento ad [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere problemi comuni negli aggiornamenti dell'applicazione facendo riferimento ai passaggi elencati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione ](service-fabric-application-upgrade-troubleshooting.md).

