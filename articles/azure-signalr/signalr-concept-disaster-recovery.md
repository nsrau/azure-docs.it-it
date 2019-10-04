---
title: Resilienza e ripristino di emergenza nel servizio Azure SignalR
description: Panoramica di come configurare più istanze del servizio SignalR per ottenere resilienza e ripristino di emergenza
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: eb70e65db4a086afc60e91cadf55a8844b102591
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61402133"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienza e ripristino di emergenza

La resilienza e il ripristino di emergenza sono un'esigenza comune per i sistemi online. Il servizio Azure SignalR garantisce già una disponibilità del 99,9%, ma è comunque un servizio a livello di area.
L'istanza del servizio sia sempre in esecuzione in un'unica area e non effettuare il failover in un'altra area quando si verifica un'interruzione a livello di area.

L'SDK del servizio offre invece una funzionalità per supportare più istanze del servizio SignalR e passare automaticamente ad altre istanze quando alcune non sono disponibili.
Con questa funzionalità si potrà eseguire il ripristino in caso di emergenza, ma si dovrà configurare autonomamente la topologia di sistema appropriata. Questo documento illustra come procedere.

## <a name="high-available-architecture-for-signalr-service"></a>Architettura a disponibilità elevata per il servizio SignalR

Per ottenere la resilienza tra aree per il servizio SignalR, è necessario configurare più istanze del servizio in aree diverse. In questo modo, quando un'area è inattiva è possibile usare le altre come backup.
Quando si connettono più istanze del servizio al server app, esistono due ruoli: primario e secondario.
Il ruolo primario è un'istanza che gestisce il traffico online, mentre quello secondario è un'istanza dotata di funzionalità complete ma di backup per il ruolo primario.
Nell'implementazione dell'SDK, la negoziazione restituirà solo gli endpoint primari, quindi normalmente i client si connettono solo agli endpoint primari.
Quando l'istanza primaria è inattiva, tuttavia, la negoziazione restituirà gli endpoint secondari affinché il client possa comunque connettersi.
Istanza primaria e il server applicazioni sono connesse tramite le connessioni al server normale ma istanza secondaria e il server applicazioni sono connessi tramite un tipo speciale di connessione denominato connessione debole.
La differenza principale di una connessione debole è di non accettare routing di connessione client, in quanto istanza secondaria si trova in un'altra area. Routing di un client a un'altra area non è una soluzione ottimale (aumenta la latenza).

Un'istanza del servizio può avere diversi ruoli se connessa a più server app.
Una configurazione tipica per uno scenario che include più aree consiste nell'avere due o più coppie di server app e istanze del servizio SignalR.
All'interno di ogni coppia, il server app e il servizio SignalR si trovano nella stessa area e il servizio SignalR è connesso al server app come ruolo primario.
Server app e servizio SignalR sono connessi anche tra le coppie, ma SignalR diventa un ruolo secondario nella connessione al server di un'altra area.

Con questa topologia, un messaggio da un server può comunque essere recapitato a tutti i client perché tutti i server app e le istanze del servizio SignalR sono interconnessi.
Al momento della connessione, tuttavia, un client viene sempre indirizzato al server app nella stessa area per ottenere una latenza di rete ottimale.

Di seguito è riportato un diagramma che illustra tale topologia:

![Topologia](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configurare i server app con più istanze del servizio SignalR

Dopo aver creato il servizio SignalR e i server app in ogni area, è possibile configurare i server app per la connessione a tutte le istanze del servizio SignalR.

Questa operazione può essere eseguita in due modi.

### <a name="through-config"></a>Tramite configurazione

È consigliabile sono noti come impostare una stringa di connessione servizio SignalR tramite settings/web.cofig/app le variabili di ambiente, tramite una voce di configurazione denominata `Azure:SignalR:ConnectionString`.
Se sono presenti più endpoint, si possono impostare in più voci di configurazione, ognuna con il formato seguente:

```
Azure:SignalR:Connection:<name>:<role>
```

Nell'esempio, `<name>` è il nome dell'endpoint e `<role>` è il relativo ruolo (primario o secondario).
Il nome è facoltativo, ma sarà utile se si vuole personalizzare ulteriormente il comportamento di routing tra più endpoint.

### <a name="through-code"></a>Tramite codice

Se si preferisce archiviare la stringa di connessione in un'altra posizione, è possibile eseguirne la lettura nel codice e usarla come parametro durante la chiamata di `AddAzureSignalR()` (in ASP.NET Core) o `MapAzureSignalR()` (in ASP.NET).

Di seguito è riportato il codice di esempio.

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>Sequenza di failover e procedure consigliate

La configurazione della topologia di sistema è ora appropriata. Ogni volta che un'istanza del servizio SignalR è inattiva, il traffico online verrà indirizzato alle altre istanze.
Quando un'istanza primaria è inattiva e viene ripristinata in un secondo momento, si verifica quanto segue:

1. L'istanza primaria del servizio è inattiva e tutte le connessioni al server di tale istanza verranno interrotte.
2. Tutti i server connessi all'istanza la contrassegneranno come offline. La negoziazione non restituirà più questo endpoint e inizierà a restituire l'endpoint secondario.
3. Verranno chiuse anche tutte le connessioni client dell'istanza e i client si riconnetteranno. Dato che i server app restituiscono l'endpoint secondario, i client si connetteranno all'istanza secondaria.
4. L'istanza secondaria gestisce ora tutto il traffico online. Tutti i messaggi dal server ai client possono comunque essere recapitati perché l'istanza secondaria è connessa a tutti i server app. I messaggi dai client al server, tuttavia, vengono indirizzati solo al server app nella stessa area.
5. Quando l'istanza primaria è ripristinata e di nuovo online, il server app ristabilisce le connessioni all'istanza e la contrassegna come online. La negoziazione restituirà di nuovo l'endpoint primario, quindi i nuovi client verranno connessi all'istanza primaria. Le connessioni client esistenti, tuttavia, non verranno interrotte e i client continueranno a essere indirizzati all'istanza secondaria finché non si disconnettono.

I diagrammi seguenti illustrano come viene effettuato il failover nel servizio SignalR:

Figura 1 - Prima del failover ![Prima del failover](media/signalr-concept-disaster-recovery/before-failover.png)

Figura 2 - Dopo il failover ![Dopo il failover](media/signalr-concept-disaster-recovery/after-failover.png)

Figura 3 - Poco dopo il ripristino dell'istanza primaria ![Poco dopo il ripristino dell'istanza primaria](media/signalr-concept-disaster-recovery/after-recover.png)

Come si può osservare, normalmente il traffico online (in blu) viene gestito solo dal server app e dal servizio SignalR primari.
Dopo il failover, diventano attivi anche il server app e il servizio SignalR secondari.
Quando il servizio SignalR primario è di nuovo online, i nuovi client si connetteranno al servizio SignalR primario. I client esistenti restano invece connessi a quello secondario, quindi il traffico viene gestito da entrambe le istanze.
Dopo la disconnessione di tutti i client esistenti, il sistema tornerà alla situazione normale (figura 1).

Per implementare un'architettura a disponibilità elevata tra più aree sono disponibili due modelli principali:

1. Il primo consiste nell'usare una coppia di server app e istanza del servizio SignalR che gestisce tutto il traffico online e un'altra coppia come backup (modello denominato attivo/passivo, illustrato nella figura 1). 
2. L'altro consiste nell'usare due o più coppie di server app e istanze del servizio SignalR, ognuna delle quali gestisce parte del traffico online e funge da backup per altre coppie (modello denominato attivo/attivo, simile alla figura 3).

Il servizio SignalR può supportare entrambi i modelli. La differenza principale risiede nel modo in cui vengono implementati i server app.
In caso di server app attivi/passivi, anche il servizio SignalR sarà attivo/passivo, perché il server app primario restituisce solo la propria istanza primaria del servizio SignalR.
In caso di server app attivi/attivi, anche il servizio SignalR sarà attivo/attivo, perché tutti i server app restituiranno le rispettive istanze del servizio SignalR e tutte le istanze potranno quindi ricevere traffico.

Notare indipendentemente da quali modelli si sceglie di usare, è necessario connettersi ogni istanza del servizio SignalR a un server di app come primario.

Dato che la connessione di SignalR è una connessione lunga, inoltre, i client subiranno interruzioni delle connessioni in caso di emergenza e failover.
Sarà necessario gestire questi casi sul lato client in modo che siano trasparenti per i clienti finali, ad esempio eseguendo la riconnessione dopo la chiusura di una connessione.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come configurare l'applicazione per ottenere la resilienza per il servizio SignalR. Per altre informazioni dettagliate sulla connessione client/server e il routing di connessione nel servizio SignalR, vedere [questo articolo](signalr-concept-internals.md) relativo agli elementi interni del servizio SignalR.

Per la scalabilità scenari come partizionamento orizzontale, che utilizzano contemporaneamente più istanze per gestire un numero elevato di connessioni, leggere [scalabilità di più istanze](signalr-howto-scale-multi-instances.md)?