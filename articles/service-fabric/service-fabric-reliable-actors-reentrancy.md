<properties
   pageTitle="Rientranza di Reliable Actors | Microsoft Azure"
   description="Introduzione alla rientranza per Reliable Actors di Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/19/2016"
   ms.author="amanbha"/>


# Rientranza di Reliable Actors
Per impostazione predefinita, il runtime di Fabric Actors consente la rientranza basata sul contesto di chiamata logico. Ciò consente agli attori di essere rientranti se si trovano nella stessa catena del contesto di chiamata. Ad esempio, l'attore A invia un messaggio all'attore B che invia un messaggio all'attore C. Durante l'elaborazione del messaggio, se l'attore C chiama l'attore A, il messaggio è rientrante e sarà quindi consentito. Tutti gli altri messaggi che fanno parte di un contesto di chiamata diverso verranno bloccati sull'attore A fino al completamento dell'elaborazione.

Gli attori che desiderano impedire la rientranza basata sul contesto di chiamata logico possono disabilitarla associando alla classe attore l'oggetto `ReentrantAttribute(ReentrancyMode.Disallowed)`.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Il codice seguente mostra la classe attore che imposta la modalità di rientranza su `ReentrancyMode.Disallowed`. In questo caso, se un attore invia un messaggio rientrante a un altro attore, verrà generata un'eccezione di tipo `FabricException`.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=AcomDC_0224_2016-->