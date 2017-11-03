---
title: Ripristino di emergenza geografico per il bus di servizio di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza nel bus di servizio di Azure
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Ripristino di emergenza geografico per il bus di servizio di Azure (anteprima)

In caso di tempo di inattività dei data center di un'area specifica, è essenziale che l'elaborazione dei dati continui in un'area diversa o in un data center diverso. Il *ripristino di emergenza geografico* e la *replica geografica* sono quindi funzionalità importanti per qualsiasi azienda. Il bus di servizio di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

L'anteprima del ripristino di emergenza geografico è attualmente disponibile solo in due aree (**Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**).

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

L'articolo [Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze](service-bus-outages-disasters.md) illustra l'importante differenza tra "interruzioni" ed "emergenze". Un'*interruzione* è l'indisponibilità temporanea del bus di servizio di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center.

Il termine *emergenza* indica la perdita permanente o a lungo termine di un'[unità di scala](service-bus-architecture.md#service-bus-scale-units) del bus di servizio o di un data center. È possibile che il data center non torni di nuovo disponibile o che rimanga inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi o di altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico del bus di servizio di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee.  

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari del ripristino di emergenza. Si noti che la funzionalità di ripristino di emergenza geografico è disponibile solo per [spazi dei nomi Premium](service-bus-premium-messaging.md). Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: stringa di connessione principale.

-  *Spazio dei nomi primario/secondario*: descrive lo spazio dei nomi corrispondente all'alias. Lo spazio dei nomi primario è "attivo" e riceve messaggi, mentre quello secondario è "passivo" e non riceve messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono accettare messaggi senza modifiche al codice dell'applicazione.

-  *Metadati*: rappresentazione di oggetti nel bus di servizio di Azure. Sono attualmente supportati solo i metadati.

-  *Failover*: processo di attivazione dello spazio dei nomi secondario. È necessario eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando risulta nuovamente disponibile e quindi eliminare lo spazio dei nomi. Per creare un altro failover, aggiungere un nuovo spazio dei nomi secondario all'associazione. Se si desidera riusare lo spazio dei nomi principale precedente dopo un failover, è necessario innanzitutto rimuovere tutte le entità esistenti dallo spazio dei nomi. Assicurarsi di ricevere tutti i messaggi prima di procedere.

## <a name="failover-workflow"></a>Flusso di lavoro di failover

La sezione seguente è una panoramica dell'intero processo di configurazione del failover iniziale e delle operazioni successive da eseguire.

![1][]

È prima di tutto necessario configurare uno spazio dei nomi primario e secondario, quindi creare un'associazione. L'associazione fornisce un alias che può essere usato per la connessione. Poiché si usa un alias, non è necessario modificare le stringhe di connessione. È possibile aggiungere solo nuovi spazi dei nomi all'associazione di failover. È infine necessario aggiungere logica di trigger, ad esempio logica di business che rileva se lo spazio dei nomi non è disponibile e avvia il failover. È possibile verificare la disponibilità dello spazio dei nomi usando la funzionalità [esplorazione dei messaggi](message-browsing.md) del bus di servizio.

Dopo avere configurato il monitoraggio e il ripristino di emergenza, è possibile esaminare il processo di failover. Se il trigger avvia un failover oppure se si avvia manualmente il failover, è necessario eseguire due passaggi:

1. È necessario potere eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un secondo spazio dei nomi passivo e aggiornare l'associazione. 
2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando il nuovo spazio dei nomi risulta disponibile. Successivamente, usare di nuovo o eliminare lo spazio dei nomi primario precedente.

![2][]

## <a name="set-up-disaster-recovery"></a>Configurare il ripristino di emergenza

Questa sezione illustra come compilare codice personalizzato per il ripristino di emergenza geografico del bus di servizio. A questo scopo, sono necessari due spazi dei nomi in posizioni indipendenti, ad esempio Stati Uniti meridionali e Stati Uniti centro-settentrionali. L'esempio seguente usa Visual Studio 2017.

1.  Creare un nuovo progetto **App console (.NET Framework)** in Visual Studio e specificare un nome, ad esempio **SBGeoDR**.

2.  Installare i pacchetti NuGet seguenti:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Assicurarsi che la versione del pacchetto NuGet Newtonsoft.Json in uso sia 10.0.3.

3.  Aggiungere le istruzioni `using` seguenti al codice:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Modificare il metodo `main()` per aggiungere due spazi dei nomi Premium:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Abilitare l'associazione tra i due spazi dei nomi e ottenere l'alias che viene usato successivamente per la connessione alle entità:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

La configurazione di due spazi dei nomi associati è stata completata. È ora possibile creare entità per osservare la sincronizzazione dei metadati. Se si vuole eseguire subito il failover, è necessario attendere il completamento della sincronizzazione dei metadati. È possibile aggiungere un breve periodo di inattività, ad esempio:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

A questo punto è possibile aggiungere entità tramite il portale o tramite Azure Resource Manager e verificare la procedura di sincronizzazione. A meno che non si preveda di eseguire manualmente il failover, è necessario creare un'app per il monitoraggio dello spazio dei nomi primario e l'avvio del failover nel caso in cui lo spazio dei nomi risulti non disponibile. 

## <a name="initiate-a-failover"></a>Avviare un failover

Il codice seguente mostra come avviare un failover:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Dopo l'attivazione del failover, aggiungere un nuovo spazio dei nomi passivo e ristabilire l'associazione. Il codice per la creazione di una nuova associazione viene mostrato nella sezione precedente. È anche necessario rimuovere i messaggi dallo spazio dei nomi primario precedente al termine del failover. Per esempi di ricezione dei messaggi da una coda, vedere [Introduzione alle code](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Come disabilitare il ripristino di emergenza geografico

Il codice seguente mostra come disabilitare un'associazione di spazi dei nomi:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

Il codice seguente elimina l'alias creato:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Passaggi dopo un failover (failback)

Dopo un failover, eseguire i due passaggi seguenti:

1.  Creare un nuovo spazio dei nomi secondario passivo. Il codice viene mostrato in una sezione precedente.
2.  Rimuovere i messaggi rimanenti dalla coda.

## <a name="alias-connection-string-and-test-code"></a>Stringa di connessione alias e codice di test

Se si vuole testare il processo di failover, è possibile scrivere un'applicazione di esempio che esegue il push di messaggi nello spazio dei nomi primario usando l'alias. A questo scopo, assicurarsi di ottenere la stringa di connessione alias da uno spazio dei nomi attivo. Con la versione di anteprima corrente non sono disponibili altre interfacce per ottenere direttamente la stringa di connessione. Il codice di esempio seguente si connette prima e dopo il failover:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'API REST per il ripristino di emergenza geografico, vedere [qui](/rest/api/servicebus/disasterrecoveryconfigs).
- Eseguire l'[esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) sul ripristino di emergenza geografico.
- Vedere l'[esempio che invia messaggi a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) per il ripristino di emergenza geografico.

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli articoli seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
