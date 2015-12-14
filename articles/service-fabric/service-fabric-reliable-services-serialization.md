<properties
   pageTitle="Serializzazione Reliable Service | Microsoft Azure"
   description="Documentazione concettuale per la serializzazione di Reliable Services di Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Serializzazione in Reliable Services
Reliable State Manager può contenere più Reliable Object. Alcuni di questi Reliable Object possono essere strutture di dati generici come Reliable Dictionary e Reliable Queue pronti all’uso. Poiché queste sono strutture di dati generici affidabili, gli oggetti generici che contengono devono essere serializzati in modo che possano essere replicati e resi persistenti su disco.

Reliable State Manager supporta tre tipi di serializzatori; * Serializzatori personalizzati, * Serializzatori integrati per un numero limitato di tipi, * DataContractSerilizer

Quando un Reliable Object deve serializzare un oggetto, esegue una query di Reliable State Manager per un serializzatore per il tipo specificato. Reliable State Manager controlla prima se esiste un serializzatore personalizzato registrato per il tipo di input. In caso contrario, verifica se uno dei serializzatori integrati può serializzare il tipo. Reliable State Manager dispone di serializzatori integrati per i seguenti tipi: guid, bool, byte, sbyte, char, decimal, double, float, int, uint, long, ulong, short, ushort e string. In caso contrario, restituisce DataContractSerializer.

Questo articolo è incentrato su quando e come utilizzare la serializzazione personalizzata.

## Quando utilizzare la serializzazione personalizzata
Esistono due motivi comuni per utilizzare la serializzazione personalizzata * Prestazioni * Crittografia

Per i tipi che non sono coperti da tipi integrati, un miglioramento notevole delle prestazioni può essere ottenuto utilizzando serializzatori compilati in modo personalizzato anziché DataContractSerializer. Uno dei motivi è che i serializzatori personalizzati non devono serializzare le informazioni sul tipo. Service Fabric garantisce che a un serializzatore di stato per un determinato tipo sarà fornito solo tale tipo da serializzare e deserializzare.

I dati serializzati generati dai serializzatori vengono replicati e resi persistenti su disco così come sono. Per i dati riservati, potrebbe essere utile per assicurarsi che i bit sulla rete e nel disco siano crittografati.

## Come utilizzare la serializzazione personalizzata
Per utilizzare un serializzatore personalizzato per un determinato tipo, è necessario * Creare un serializzatore di stato personalizzato * Registrare il serializzatore di stato personalizzato nel Reliable Service

### Come implementare un serializzatore personalizzato
I serializzatori personalizzati devono implementare l'interfaccia IStateSerializer<T>. I due metodi principali in questa interfaccia sono * T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter);

Il primo viene utilizzato dal ReliableObject per leggere l'oggetto serializzato da un flusso utilizzando un BinaryReader. Il secondo viene utilizzato per l'operazione inversa: scrivere l'oggetto in un flusso usando un Binary Writer.

Di seguito è riportata una classe personalizzata di esempio e un relativo serializzatore.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]Nell'esempio precedente, l'implementazione degli overload Read e Write si limita a chiamare gli overload della controparte. Questo avviene perché i due metodi seguenti vengono utilizzati per una funzionalità che non è ancora disponibile.

### Come registrare un serializzatore personalizzato
Per registrare un serializzatore personalizzato, è innanzitutto necessario un metodo che possa registrare tutti i serializzatori personalizzati. Questo metodo deve non accettare argomenti ed essere in grado di restituire un'attività. In questo metodo IReliableStateManager.TryAddStateSerializer<T> deve essere utilizzato per registrare tutti i serializzatori personalizzati per il Reliable Service.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

Il passaggio successivo consiste nel registrare il metodo sopra indicato come un delegato da chiamare da parte del Reliable State Manager quando è necessario registrare tutti i serializzatori di stato personalizzati. Questo metodo viene chiamato solo all'inizio della replica del Reliable Service prima di avviare il ripristino locale, poiché i serializzatori potrebbero essere necessari per leggere i dati serializzati dal disco. Una volta registrato il serializzatore, il tipo rilevante da tutti i Reliable Object utilizzerà questo serializzatore per serializzare e deserializzare i loro oggetti.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Controllo delle versioni
Service Fabric prevede che i serializzatori siano compatibili all’infinito con le versioni successive e precedenti. Per i tipi che utilizzano serializzatori integrati, Service Fabric assicura la compatibilità con le versioni successive e precedenti. Per i tipi che usano DataContractSerializer o un serializzatore personalizzato, l’utente non deve mai eseguire una modifica sostanziale. Per il controllo delle versioni di DataContract vedere [Controllo delle versioni di DataContract](https://msdn.microsoft.com/library/ms731138.aspx). Se è necessaria una modifica sostanziale, lo stato deve essere spostato da un'istanza del servizio con la versione dei dati precedente a un servizio con la nuova versione dei dati a livello di applicazione.

### Quando si usa un serializzatore
 * Scrivere operazioni sui Reliable Object ne provoca la serializzazione, la replica e la memorizzazione in un log.
 * Dopo la registrazione di un numero sufficiente di operazioni, i dati più recenti dalla memoria vengono serializzati e salvati su disco.
 * Per ricompilare una replica, i file salvati sul disco e i dati recenti dal log vengono inviati direttamente byte per byte (ad esempio i dati non vengono nuovamente serializzati) da una replica primaria. Questi byte vengono deserializzati in oggetti C# nella replica secondaria.
 * Durante il ripristino, il file del checkpoint e i log dei dati recenti vengono deserializzati.
 * Durante il backup, i file del checkpoint e i dati dei log recenti vengono copiati byte per byte.
 * Durante il ripristino, i file del checkpoint del backup precedente e i dati di log vengono copiati nuovamente nella rispettiva posizione e vengono deserializzati.

## Passaggi successivi
 * [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->