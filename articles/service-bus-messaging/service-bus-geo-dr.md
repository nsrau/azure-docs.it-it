---
title: Ripristino di emergenza geografico per il bus di servizio di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza nel bus di servizio di Azure
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190155"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Ripristino di emergenza geografico per il bus di servizio di Azure

In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate [zone di disponibilità](../availability-zones/az-overview.md)), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il *ripristino di emergenza geografico*, quindi, è una funzionalità importante per qualsiasi azienda. Il bus di servizio di Azure supporta il ripristino di emergenza geografico a livello di spazio dei nomi.

La funzionalità di ripristino di emergenza geografico è disponibile a livello globale per lo SKU Premium del bus di servizio. 

>[!NOTE]
> Questa funzionalità assicura solo che i metadati (code, argomenti, sottoscrizioni e filtri) vengano copiati dallo spazio dei nomi primario a quello secondario, se sono abbinati.

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

È importante notare la distinzione tra "interruzioni" ed "emergenze". 

Un'*interruzione* è l'indisponibilità temporanea del bus di servizio di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center. Alcune interruzioni sono solo brevi perdite di connessione dovute a problemi di rete o temporanei. 

Il termine *emergenza* indica la perdita permanente o a lungo termine di un cluster del bus di servizio, un'area di Azure o un data center. L'area o il data center potrebbe o meno tornare di nuovo disponibile oppure potrebbe rimanere inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi, eventi o altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico del bus di servizio di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee. Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari di ripristino di emergenza. Si noti che la funzionalità di ripristino di emergenza geografico è disponibile solo per lo [SKU Premium](service-bus-premium-messaging.md). Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi. L'uso di un alias garantisce che la stringa di connessione rimanga invariata quando viene attivato il failover.

-  *Spazio dei nomi primario/secondario*: spazi dei nomi corrispondenti all'alias. Lo spazio dei nomi primario è "attivo" e riceve i messaggi (può essere uno spazio dei nomi esistente o nuovo). Lo spazio dei nomi secondario è "passivo" e non riceve i messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono facilmente accettare messaggi senza modifiche al codice dell'applicazione o alla stringa di connessione. Per fare in modo che solo lo spazio dei nomi attivo riceva i messaggi, è necessario usare l'alias. 

-  *Metadati*: entità come code, argomenti e sottoscrizioni e le relative proprietà del servizio associate allo spazio dei nomi. Si noti che solo le entità e le relative impostazioni vengono replicate automaticamente. I messaggi non vengono replicati.

-  *Failover*: processo di attivazione dello spazio dei nomi secondario.

## <a name="setup"></a>Configurazione

Di seguito viene fornita una panoramica della procedura di configurazione dell'associazione tra gli spazi dei nomi.

![1][]

Il processo di configurazione si articola nelle fasi seguenti:

1. Effettuare il provisioning di uno spazio dei nomi Premium ***primario*** del bus di servizio.

2. Effettuare il provisioning di uno spazio dei nomi Premium ***secondario*** del bus di servizio in un'area *diversa da quella in cui è stato eseguito il provisioning dello spazio dei nomi primario*. In questo modo è possibile attivare l'isolamento dei guasti tra aree di data center diverse.

3. Creare l'associazione tra lo spazio dei nomi primario e quello secondario in modo da ottenere l'***alias***.

    >[!NOTE] 
    > Se hai [eseguita la migrazione dello spazio dei nomi Service Bus di Azure Standard a Premium del Bus di servizio di Azure](service-bus-migrate-standard-premium.md), sarà necessario usare l'alias preesistente (ad esempio il Bus di servizio Standard dello spazio dei nomi stringa di connessione) per creare il ripristino di emergenza configurazione tramite il **PS/CLI** oppure **API REST**.
    >
    >
    > Questo avviene perché, durante la migrazione, il nome di tipo DNS/stringa di connessione dello spazio dei nomi Standard del Bus di servizio Azure stesso diventa un alias per lo spazio dei nomi Premium del Bus di servizio di Azure.
    >
    > Le applicazioni client devono usare questo alias (vale a dire Standard del Bus di servizio Azure dello spazio dei nomi stringa di connessione) per connettersi allo spazio dei nomi Premium in cui l'associazione di ripristino di emergenza è stata configurata.
    >
    > Se si usa il portale per configurare la configurazione di ripristino di emergenza, il portale astrarrà questa avvertenza da parte dell'utente.


4. Usare l'***alias*** ottenuto nel passaggio 3 per connettere le applicazioni client allo spazio dei nomi primario abilitato per il ripristino di emergenza geografico. Inizialmente, l'alias fa riferimento allo spazio dei nomi primario.

5. [Facoltativo] Aggiungere funzionalità di monitoraggio per rilevare i casi in cui è necessario un failover.

## <a name="failover-flow"></a>Flusso del failover

Un failover viene attivato manualmente dal cliente (sia in modo esplicito con un comando o tramite la logica di business di proprietà del client che attiva il comando) e mai da Azure. In questo modo viene concessa al cliente la proprietà completa e la visibilità per la risoluzione di interruzioni sulla rete backbone di Azure.

![4][]

In seguito all'attivazione del failover:

1. La stringa di connessione ***alias*** viene aggiornata in modo che faccia riferimento allo spazio dei nomi Premium secondario.

2. I client (mittenti e destinatari) si connettono automaticamente allo spazio dei nomi secondario.

3. L'associazione esistente tra lo spazio dei nomi Premium primario e quello secondario viene interrotta.

Dopo aver avviato il failover:

1. È necessario poter eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un altro spazio dei nomi passivo e aggiornare l'associazione. 

2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando è di nuovo disponibile. Successivamente, usare tale spazio dei nomi per la messaggistica regolare di fuori della configurazione del ripristino geografico oppure eliminare lo spazio dei nomi primario precedente.

> [!NOTE]
> È supportata solo la semantica di inoltro in caso di errore. In questo scenario, si esegue il failover e quindi si esegue di nuovo l'associazione con un nuovo spazio dei nomi. Il failback, ad esempio in un cluster SQL, non è supportato. 

È possibile automatizzare il failover con sistemi di monitoraggio o con soluzioni di monitoraggio personalizzate. Tale automazione, tuttavia, richiede pianificazione e lavoro aggiuntivi che esulano dall'ambito di questo articolo.

![2][]

## <a name="management"></a>Gestione

Se si commette un errore, ad esempio associando le aree non corrette durante la configurazione iniziale, è possibile interrompere l'associazione dei due spazi dei nomi in qualsiasi momento. Per usare gli spazi dei nomi associati come normali spazi dei nomi, eliminare l'alias.

## <a name="use-existing-namespace-as-alias"></a>Usare lo spazio dei nomi esistente come alias

Se si ha uno scenario in cui non è possibile modificare le connessioni di producer e consumer, è possibile riutilizzare il nome dello spazio dei nomi come nome dell'alias. Vedere il [codice di esempio su GitHub qui](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Esempi

Gli [esempi su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostrano come configurare e avviare un failover. Questi esempi illustrano i concetti seguenti:

- Un esempio .NET e le impostazioni necessarie in Azure Active Directory per usare Azure Resource Manager con il bus di servizio per configurare e abilitare il ripristino di emergenza geografico.
- Passaggi necessari per eseguire il codice di esempio.
- Modalità di utilizzo di uno spazio dei nomi esistente come alias.
- I passaggi per abilitare alternativamente il ripristino di emergenza geografico tramite PowerShell o l'interfaccia della riga di comando.
- [L'invio e la ricezione](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) dallo spazio dei nomi primario o secondario corrente tramite l'alias.

## <a name="considerations"></a>Considerazioni

Tenere presente le considerazioni seguenti per questa versione:

1. Quando si pianifica il failover, è consigliabile considerare anche il fattore tempo. Ad esempio, se si perde la connettività per più di 15-20 minuti, è possibile decidere di avviare il failover.

2. Il fatto che non vengano replicati dati significa che le sessioni attive non vengono replicate. Il rilevamento dei duplicati e i messaggi pianificati potrebbero inoltre non funzionare. Le nuove sessioni, i nuovi messaggi pianificati e i nuovi duplicati funzioneranno. 

3. È necessario [provare a effettuare](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) il failover di un'infrastruttura distribuita complessa almeno una volta.

4. La sincronizzazione delle entità può richiedere tempo, circa un minuto per 50-100 entità. Anche le sottoscrizioni e le regole contano come entità.

## <a name="availability-zones"></a>Zone di disponibilità

Lo SKU Premium del bus di servizio supporta anche le [zone di disponibilità](../availability-zones/az-overview.md) fornendo località con isolamento di errore all'interno di un'area di Azure.

> [!NOTE]
> Il supporto per le zone di disponibilità per il bus di servizio di Azure Premium è disponibile solo nelle [aree di Azure](../availability-zones/az-overview.md#services-support-by-region) in cui sono presenti le zone di disponibilità.

Usando il portale di Azure, è possibile abilitare le zone di disponibilità solo negli spazi dei nomi. Il bus di servizio non supporta la migrazione degli spazi dei nomi esistenti. Non è possibile disabilitare la ridondanza della zona dopo che è stata abilitata nello spazio dei nomi.

![3][]

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'API REST per il ripristino di emergenza geografico, vedere [qui](/rest/api/servicebus/disasterrecoveryconfigs).
- Eseguire l'[esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) sul ripristino di emergenza geografico.
- Vedere l'[esempio che invia messaggi a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) per il ripristino di emergenza geografico.

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli articoli seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
