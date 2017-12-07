---
title: Eventi pianificati per macchine virtuali Windows in Azure | Microsoft Docs
description: Eventi pianificati usando il servizio metadati di Azure per le macchine virtuali Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 2b873501085ba2d293be564009b5d5daccbf9c1e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-windows-vms"></a>Servizio metadati di Azure: eventi pianificati (anteprima) per VM Windows

> [!NOTE] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Gli eventi pianificati sono un servizio metadati di Azure che concede all'applicazione il tempo di prepararsi alla manutenzione delle macchine virtuali. Il servizio offre informazioni sugli eventi di manutenzione imminenti (ad esempio il riavvio), in modo che l'applicazione possa prepararsi a questi e limitare le interruzioni. Il servizio è disponibile per tutti i tipi di macchine virtuali di Azure, incluse le infrastrutture PaaS e IaaS, sia Windows che Linux. 

Per informazioni su Eventi pianificati in Linux, vedere [Eventi pianificati per macchine virtuali Linux](../linux/scheduled-events.md).

## <a name="why-scheduled-events"></a>Perché usare gli eventi pianificati

Per molte applicazioni è un vantaggio avere tempo per prepararsi alla manutenzione delle macchine virtuali. Questo tempo può essere impiegato in attività specifiche dell'applicazione per ottenere un miglioramento della disponibilità, dell'affidabilità e della gestibilità, ad esempio: 

- Checkpoint e ripristino
- Esaurimento delle connessioni
- Failover della replica primaria 
- Rimozione dal pool di bilanciamento del carico
- Registrazione eventi
- Arresto normale 

Tramite gli eventi pianificati l'applicazione è in grado di sapere quando verrà eseguita la manutenzione e di attivare attività specifiche per limitarne l'impatto.  

Gli eventi pianificati informano sugli eventi nei casi d'uso seguenti:
- Manutenzione avviata dalla piattaforma (ad esempio l'aggiornamento del sistema operativo host)
- Manutenzione avviata dall'utente (ad esempio il riavvio o la ridistribuzione di una macchina virtuale eseguita dall'utente)

## <a name="the-basics"></a>Nozioni di base  

Il Servizio metadati di Azure presenta informazioni sulle macchine virtuali in esecuzione usando un endpoint REST accessibile dall'interno della macchina virtuale. Le informazioni sono disponibili tramite un indirizzo IP non instradabile, in modo da non essere esposte al di fuori della macchina virtuale.

### <a name="scope"></a>Scope
Gli eventi pianificati vengono recapitati a:
- Tutte le macchine virtuali in un servizio cloud
- Tutte le macchine virtuali in un set di disponibilità
- Tutte le macchine virtuali in un gruppo di posizionamento di un set di scalabilità. 

Pertanto, è consigliabile controllare il campo `Resources` nell'evento per individuare le macchine virtuali che ne saranno interessate. 

## <a name="discovering-the-endpoint"></a>Individuazione dell'endpoint
Per le macchine virtuali abilitate per le reti virtuali, l'endpoint completo per la versione più recente di eventi pianificati è: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Nel caso in cui venga creata una macchina virtuale in una rete virtuale, il servizio di metadati è disponibile all'indirizzo IP statico non instradabile, `169.254.169.254`.
Se la macchina virtuale non viene creata all'interno di una rete virtuale, caso predefinito per i servizi cloud e le macchine virtuali classiche, è necessaria logica aggiuntiva per individuare l'indirizzo IP da usare. Fare riferimento a questo esempio per informazioni su come [individuare l'endpoint dell'host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Controllo delle versioni 
Il servizio eventi pianificati è un servizio con versione. Le versioni sono obbligatorie e la versione corrente è `2017-08-01`.

| Versione | Note sulla versione | 
| - | - | 
| 2017-08-01 | <li> È stato rimosso il carattere di sottolineatura all'inizio dei nomi delle risorse per le macchine virtuali IaaS<br><li>Requisito dell'intestazione dei metadati applicato per tutte le richieste | 
| 2017-03-01 | <li>Versione di anteprima pubblica

> [!NOTE] 
> Le versioni precedenti di anteprima di eventi pianificati {ultima} sono supportate come versione dell'API. Questo formato non è più supportato e verrà rimosso in futuro.

### <a name="using-headers"></a>Uso delle intestazioni
Quando si eseguono query sul Servizio metadati, è necessario specificare l'intestazione `Metadata:true` per garantire che la richiesta non sia stata reindirizzata accidentalmente. L'intestazione `Metadata:true` è obbligatoria per tutte le richieste di eventi pianificati. Se non si include l'intestazione nella richiesta, il servizio metadati restituisce una risposta Richiesta non valida.

### <a name="enabling-scheduled-events"></a>Attivazione degli eventi pianificati
La prima volta che si emette una richiesta per gli eventi pianificati, Azure abilita in modo implicito la funzionalità nella macchina virtuale. Di conseguenza la prima chiamata potrebbe ricevere una risposta con un ritardo massimo di due minuti.

> [!NOTE]
> Gli eventi pianificati vengono disabilitati automaticamente per il servizio se quest'ultimo non chiama l'endpoint per 1 giorno. Dopo che gli eventi pianificati sono stati disabilitati per il servizio, non verranno creati eventi per la manutenzione avviata dall'utente.

### <a name="user-initiated-maintenance"></a>Manutenzione avviata dall'utente
La manutenzione delle macchine virtuali avviata dall'utente tramite il portale, l'API o l'interfaccia della riga di comando di Azure oppure tramite PowerShell restituisce un evento pianificato. In questo modo è possibile testare la logica di preparazione della manutenzione dell'applicazione e permettere all'applicazione di prepararsi per la manutenzione avviata dall'utente.

Il riavvio di una macchina virtuale pianifica un evento di tipo `Reboot`. La ridistribuzione di una macchina virtuale pianifica un evento di tipo `Redeploy`.

> [!NOTE] 
> Attualmente è possibile pianificare un massimo di 100 operazioni di manutenzione avviate dall'utente.

> [!NOTE] 
> Attualmente la manutenzione avviata dall'utente che dà luogo a eventi pianificati non è configurabile. Questa funzionalità è pianificata per una versione successiva.

## <a name="using-the-api"></a>Uso dell'API

### <a name="query-for-events"></a>Query per gli eventi
È possibile eseguire query per gli eventi pianificati semplicemente eseguendo la chiamata seguente:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Una risposta contiene una serie di eventi pianificati. Una serie vuota indica che al momento non sono presenti eventi pianificati.
Nel caso in cui siano presenti eventi pianificati, la risposta contiene una serie di eventi: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Proprietà degli eventi
|Proprietà  |  Descrizione |
| - | - |
| EventId | Identificatore globalmente univoco per l'evento. <br><br> Esempio: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impatto che l'evento causa. <br><br> Valori: <br><ul><li> `Freeze`: è pianificata una sospensione della macchina virtuale per alcuni secondi. La CPU viene sospesa, ma la memoria, i file aperti o le connessioni di rete non subiranno conseguenze. <li>`Reboot`: è pianificato un riavvio della macchina virtuale. La memoria non permanente andrà persa. <li>`Redeploy`: è pianificato uno spostamento della macchina virtuale in un altro nodo. I dischi temporanei andranno persi. |
| ResourceType | Tipo di risorsa su cui l'evento influisce. <br><br> Valori: <ul><li>`VirtualMachine`|
| Risorse| Elenco delle risorse su cui l'evento influisce. Contiene sicuramente i computer per al massimo un [Dominio di aggiornamento](manage-availability.md), ma non può contenere tutti i computer nel dominio di aggiornamento. <br><br> Esempio: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Event Status | Stato dell'evento. <br><br> Valori: <ul><li>`Scheduled`: l'avvio dell'evento è pianificato in seguito al tempo specificato nella proprietà `NotBefore`.<li>`Started`: l'evento si è avviato.</ul> Non viene indicato `Completed` o uno stato simile; l'evento non verrà più restituito al suo completamento.
| NotBefore| Tempo dopo il quale l'evento può essere avviato. <br><br> Esempio: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Pianificazione degli eventi
Ogni evento è pianificato con un ritardo minimo che dipende dal tipo di evento. Questo tempo si riflette in una proprietà `NotBefore` dell'evento. 

|EventType  | Minimum Notice |
| - | - |
| Freeze| 15 minuti |
| Reboot | 15 minuti |
| Ripetere la distribuzione | 10 minuti |

### <a name="starting-an-event"></a>Avvio di un evento 

Dopo aver appreso informazioni su un evento imminente e aver completato la logica per l'arresto normale, è possibile approvare l'evento in sospeso facendo una chiamata `POST` al servizio metadati con `EventId`. Ciò indica ad Azure che si può ridurre il tempo di notifica minimo, quando possibile. 

Di seguito è riportato il codice json previsto nel corpo della richiesta `POST`. La richiesta deve contenere un elenco di `StartRequests`. Ogni `StartRequest` contiene l'`EventId` per l'evento che si vuole accelerare:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Il riconoscimento di un evento consente all'evento di procedere per tutti gli elementi `Resources` nell'evento, non solo per la macchina virtuale che riconosce l'evento. Pertanto è possibile scegliere un coordinatore che si occupi del riconoscimento, che potrebbe essere semplicemente il primo nel campo `Resources`.


## <a name="powershell-sample"></a>Esempio PowerShell 

L'esempio seguente esegue query sul servizio metadati per gli eventi pianificati e quindi approva gli eventi in sospeso.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Passaggi successivi 

- Esaminare gli esempi di codice di eventi pianificati nel [repository di Github per gli eventi pianificati di Azure per i metadati delle istanze](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Altre informazioni sulle API disponibili nel [servizio metadati dell'istanza](instance-metadata-service.md).
- Informazioni sulla [manutenzione pianificata per le macchine virtuali Windows in Azure](planned-maintenance.md).
