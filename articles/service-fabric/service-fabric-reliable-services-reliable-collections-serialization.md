---
title: Serializzazione dell'oggetto Reliable Collection
description: Informazioni sulla serializzazione degli oggetti Reliable Collections di Azure Service Fabric, inclusa la strategia predefinita e come definire la serializzazione personalizzata.'
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639548"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializzazione di un oggetto Reliable Collections in Azure Service Fabric
Le raccolte Reliable Collections replicano e mantengono i propri elementi per garantirne la persistenza anche in caso di errori del computer o interruzioni dell'alimentazione.
Per replicare e rendere persistenti gli elementi, le raccolte Reliable Collections devono serializzarli.

Le raccolte Reliable Collections ottengono il serializzatore appropriato per un determinato tipo da Reliable State Manager.
Reliable State Manager contiene serializzatori predefiniti e consente la registrazione di serializzatori personalizzati per un determinato tipo.

## <a name="built-in-serializers"></a>Serializzatori predefiniti

Reliable State Manager include serializzatori predefiniti per alcuni tipi comuni, così da consentirne una serializzazione efficiente per impostazione predefinita. Per altri tipi, Reliable State Manager esegue il fallback per l'uso di [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
I serializzatori predefiniti risultano più efficienti perché sanno che i loro tipi non possono essere modificati e non hanno bisogno di includere informazioni sul tipo quali il nome.

Reliable State Manager dispone di un serializzatore predefinito per i tipi seguenti: 
- Guid
- bool
- byte
- sbyte
- byte[]
- char
- string
- decimal
- double
- float
- INT
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Serializzazione personalizzata

I serializzatori personalizzati vengono comunemente usati per migliorare le prestazioni o per crittografare i dati in rete e su disco. Tra le altre ragioni, i serializzatori personalizzati sono in genere più efficienti dei serializzatori generici poiché questi non devono serializzare le informazioni sul tipo. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) viene utilizzato per registrare un serializzatore personalizzato per il tipo specificato T. Questa registrazione deve avvenire nella costruzione di StatefulServiceBase per garantire che prima dell'avvio del ripristino tutte le raccolte Reliable Collections abbiano accesso al serializzatore pertinente per leggere i dati persistenti.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> I serializzatori personalizzati hanno la precedenza su quelli predefiniti. Ad esempio, quando viene registrato un serializzatore personalizzato per int, tale serializzatore viene usato per serializzare i valori integer al posto del serializzatore predefinito per int.

### <a name="how-to-implement-a-custom-serializer"></a>Come implementare un serializzatore personalizzato

Un serializzatore personalizzato deve implementare l'interfaccia [T>\<IStateSerializer.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> IStateSerializer\<T> include un overload per Write and Read che accetta un valore di base T aggiuntivo denominato. Questa API è per la serializzazione differenziale. Attualmente la funzionalità di serializzazione differenziale non è esposta. Di conseguenza, questi due overload non vengono chiamati fino a quando la serializzazione differenziale non è esposta e abilitata.

Di seguito è riportato un esempio di un tipo personalizzato denominato OrderKey che contiene quattro proprietà

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Di seguito è riportato\<un esempio di implementazione di IStateSerializer OrderKey>.
Si noti che gli overload Write e Read che accettano baseValue chiamano il rispettivo overload per la compatibilità di inoltro.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Aggiornamento
In un [aggiornamento in sequenza di un'applicazione](service-fabric-application-upgrade.md)l'aggiornamento viene applicato a un subset di nodi, procedendo con un dominio di aggiornamento per volta. Durante questo processo, alcuni domini di aggiornamento si troveranno con la versione dell'applicazione più recente e altri con quella meno recente. Nella fase di distribuzione, la versione dell'applicazione più recente deve essere in grado di leggere la versione dei dati meno recente e viceversa. Se il formato dei dati non è compatibile con le versioni successive e precedenti, è possibile che l'aggiornamento abbia esito negativo o, peggio ancora, che i dati vengano persi o danneggiati.

Se si usano serializzatori predefiniti, non esistono problemi di compatibilità.
Tuttavia, se si usa un serializzatore personalizzato o DataContractSerializer, i dati devono essere compatibili all'infinito con le versioni precedenti e successive.
In altri termini, ogni versione del serializzatore deve essere in grado di serializzare e deserializzare qualsiasi versione del tipo.

Gli utenti del contratto di dati devono seguire regole ben definite di controllo delle versioni per l'aggiunta, la rimozione e la modifica di campi. Il contratto di dati supporta inoltre l'eredità delle classi, la gestione dei campi sconosciuti e l'uso di hook nel processo di serializzazione e deserializzazione. Per altre informazioni, vedere [Utilizzo di contratti dati](https://msdn.microsoft.com/library/ms733127.aspx).

Gli utenti di serializzatori personalizzati devono seguire le linee guida del serializzatore in uso per verificarne la compatibilità con le versioni precedenti e successive.
Un modo comune per supportare tutte le versioni consiste nell'aggiungere le informazioni sulle dimensioni all'inizio e nell'aggiungere soltanto proprietà facoltative.
In questo modo ogni versione può leggere tutto quanto è in grado di leggere, saltando la rimanente parte del flusso.

## <a name="next-steps"></a>Passaggi successivi
  * [Serializzazione e aggiornamento](service-fabric-application-upgrade-data-serialization.md)
  * [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.
  * [L'aggiornamento dell'applicazione tramite Powershell](service-fabric-application-upgrade-tutorial-powershell.md) illustra l'aggiornamento di un'applicazione tramite PowerShell.Upgrading your Application Using Powershell walks you through an application upgrade using PowerShell.
  * Controllare la modalità di aggiornamento dell'applicazione utilizzando [i parametri](service-fabric-application-upgrade-parameters.md)di aggiornamento .
  * Per informazioni su come utilizzare le funzionalità avanzate durante l'aggiornamento dell'applicazione, fare riferimento ad [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).
  * Risolvere i problemi comuni negli aggiornamenti delle applicazioni facendo riferimento alla procedura descritta in [Risoluzione dei problemi](service-fabric-application-upgrade-troubleshooting.md)relativi agli aggiornamenti delle applicazioni .
