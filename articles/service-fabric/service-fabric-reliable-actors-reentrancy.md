<properties
   pageTitle="Reentrancy di Azure Service Fabric Actors"
   description="Introduzione alla reentrancy per Azure Service Fabric Actors"
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
   ms.date="03/17/2015"
   ms.author="amanbha"/>


# Reentrancy degli attori
Per impostazione predefinita, Fabric Actors consente la reentrancy basata sul contesto di chiamata logico. Ciò consente agli attori di essere rientranti se si trovano nella stessa catena del contesto di chiamata. Se ad esempio l'attore A invia un messaggio all'attore B che invia un messaggio all'attore C e durante l'elaborazione del messaggio l'attore C chiama l'attore A, il messaggio è rientrante e sarà quindi consentito. Tutti gli altri messaggi che fanno parte di un contesto di chiamata diverso verranno bloccati sull'attore A fino al completamento dell'elaborazione.

Gli attori che desiderano impedire la reentrancy basata sul contesto di chiamata logico possono disabilitarla associando alla classe attore l'oggetto `ReentrantAttribute(ReentrancyMode.Disallowed)`.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Il codice seguente mostra la classe attore che imposta la modalità di reentrancy su `ReentrancyMode.Disallowed`. In questo caso se un attore invia un messaggio rientrante a un altro attore, verrà generata un'eccezione di tipo `FabricException`.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```
 

<!---HONumber=July15_HO2-->