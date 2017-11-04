---
title: Ripristino di emergenza geografico nel servizio Hub eventi di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza servizio Hub eventi di Azure
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Ripristino di emergenza geografico nel servizio Hub eventi di Azure (anteprima)

In caso di tempo di inattività dei data center di un'area specifica, è essenziale che l'elaborazione dei dati continui in un'area diversa o in un data center diverso. Il *ripristino di emergenza geografico* e la *replica geografica* sono quindi funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

La funzionalità di ripristino di emergenza geografico del servizio Hub eventi di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee.

Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminologia

**Associazione**: lo spazio dei nomi primario è definito come *attivo* e riceve messaggi. Lo spazio dei nomi di failover è definito come *passivo* e non riceve messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono accettare messaggi senza modifiche al codice dell'applicazione. La definizione della configurazione del ripristino di emergenza tra area attiva e area passiva viene definita *associazione* delle aree.

**Alias**: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi.

**Metadati**: fa riferimento a nomi di hub eventi, gruppi di consumer, partizioni, unità elaborate, entità e proprietà associati allo spazio dei nomi.

## <a name="enable-geo-disaster-recovery"></a>Abilitare il ripristino di emergenza geografico

La procedura di abilitazione del ripristino di emergenza geografico nel servizio Hub eventi si articola in tre passaggi: 

1. Creare un'associazione geografica (con la conseguente creazione di una stringa di connessione alias e disponibilità della replica dei metadati in tempo reale). 
2. Aggiornare le stringhe di connessione client esistenti in base all'alias creato nel passaggio 1.
3. Avviare il failover: l'associazione geografica viene interrotta e l'alias fa riferimento allo spazio dei nomi secondario come nuovo spazio dei nomi primario.

Nella figura seguente è illustrato questo flusso di lavoro:

![Flusso relativo all'associazione geografica][1] 

### <a name="step-1-create-a-geo-pairing"></a>Passaggio 1: Creare un'associazione geografica

Per creare un'associazione tra due aree, è necessario uno spazio dei nomi primario e uno spazio dei nomi secondario. Sarà quindi possibile creare un alias per creare l'associazione geografica. Dopo aver associato gli spazi dei nomi a un alias, i metadati vengono replicati periodicamente in entrambi gli spazi dei nomi. 

Nel codice seguente viene illustrato come eseguire questa operazione:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Passaggio 2: Aggiornare le stringhe di connessione client esistenti

Dopo aver completato l'associazione geografica, le stringhe di connessione che fanno riferimento agli spazi dei nomi primari devono essere aggiornate in modo da fare riferimento alla stringa di connessione alias. Recuperare le stringhe di connessione come illustrato nell'esempio seguente:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Passaggio 3: Avviare un failover

Se si verifica un'emergenza o se si decide di avviare un failover sullo spazio dei nomi secondario, i metadati e dati vengono trasferiti allo spazio dei nomi secondario. Poiché le applicazioni usano le stringhe di connessione alias, non sono richieste altre azioni dal momento che iniziano automaticamente a leggere e scrivere negli hub eventi nello spazio dei nomi secondario. 

Il codice seguente mostra come attivare il failover:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Al completamento del failover, se i dati devono essere presenti nello spazio dei nomi primario, per estrarre i dati è necessario usare una stringa di connessione esplicita per gli hub eventi nello spazio dei nomi primario.

### <a name="other-operations-optional"></a>Altre operazioni (facoltativo)

È anche possibile interrompere l'associazione geografica o eliminare un alias, come illustrato nel codice seguente. Si noti che per eliminare una stringa di connessione alias, è prima necessario interrompere l'associazione geografica:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Considerazioni per l'anteprima pubblica

Tenere presente le considerazioni seguenti per questa versione:

1. La funzionalità del ripristino di emergenza geografico è disponibile solo nelle aree Stati Uniti centro-settentrionali e Stati Uniti centro-meridionali. 
2. La funzionalità è supportata solo per i nuovi spazi dei nomi.
3. Per la versione di anteprima è abilitata solo la replica dei metadati. I dati effettivi non vengono replicati.
4. Con la versione di anteprima non è previsto alcun costo per l'abilitazione della funzionalità. Tuttavia, gli spazi dei nomi primario e secondario daranno entrambi luogo ad addebiti per le unità elaborate riservate.

## <a name="next-steps"></a>Passaggi successivi

* L'[esempio disponibile in GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) illustra in dettaglio un semplice flusso di lavoro per la creazione di un'associazione geografica e l'avvio di un failover per uno scenario di ripristino di emergenza.
* Nella [informazioni di riferimento sulle API del servizio REST](/rest/api/eventhub/disasterrecoveryconfigs) sono descritte le API che consentono di eseguire la configurazione del ripristino di emergenza geografico.

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

