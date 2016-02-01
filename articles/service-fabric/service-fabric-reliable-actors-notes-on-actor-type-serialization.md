<properties
   pageTitle="Note sulla serializzazione dei tipi di attori di Reliable Actors | Microsoft Azure"
   description="Illustra i requisiti di base per la definizione delle classi serializzabili che possono essere usate per definire le interfacce e lo stato di Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Note sulla serializzazione dei tipi di Service Fabric Reliable Actors

È necessario tenere presenti alcuni aspetti importanti quando si definisce l'interfaccia e lo stato di un attore. I tipi devono essere serializzabili in base al contratto dati. Altre informazioni sui contratti dati sono disponibili in [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Tipi di interfaccia dell'attore

Gli argomenti di tutti i metodi e i tipi di risultati dell'attività restituiti da ogni metodo definito nell'[interfaccia dell'attore](service-fabric-reliable-actors-introduction.md#actors) devono essere serializzabili in base al contratto dati. Questo vale anche per gli argomenti dei metodi definiti nelle [interfacce degli eventi dell'attore](service-fabric-reliable-actors-events.md#actor-events). I metodi di interfaccia degli eventi dell'attore restituiscono sempre un valore void. Ad esempio, se l'interfaccia `IVoiceMail` definisce un metodo come:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` è un tipo .NET standard già serializzabile in base al contratto dati. Il tipo `Voicemail` deve essere serializzabile in base al contratto dati.

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## Classe relativa allo stato dell'attore

Lo stato dell'attore deve essere serializzabile in base al contratto dati. Ad esempio, una definizione di classe dell'attore può essere simile alla seguente:

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

La classe dello stato sta per essere definita con la classe e i relativi membri annotati, rispettivamente, con gli attributi **DataContract** e **DataMember**.

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=AcomDC_0121_2016-->