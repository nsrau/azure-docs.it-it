---
title: Eventi pianificati per macchine virtuali Linux in Azure | Microsoft Docs
description: Eventi pianificati usando il servizio metadati di Azure per le macchine virtuali Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: af0f986aeba11cd0b1c9399138ba1b2fe1fc848f
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Servizio metadati di Azure: eventi pianificati (anteprima) per VM Linux

> [!NOTE] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Eventi pianificati è uno dei servizi secondari del Servizio metadati di Azure. Presenta informazioni sugli eventi imminenti, ad esempio i riavvii, in modo che l'applicazione possa prepararsi di conseguenza e limitare le interruzioni. Il servizio è disponibile per tutti i tipi di macchine virtuali di Azure, inclusi PaaS e IaaS. Il servizio dà alla macchina virtuale il tempo sufficiente per eseguire attività preventive e ridurre al minimo l'effetto di un evento. 

## <a name="why-scheduled-events"></a>Perché usare gli eventi pianificati

Con Eventi pianificati, è possibile eseguire la procedura per ridurre l'impatto della manutenzione avviata dalla piattaforma o delle azioni avviate dall'utente nel servizio. 

I carichi di lavoro a più istanze, che usano tecniche di replica per mantenere lo stato, potrebbero essere soggetti a interruzioni tra più istanze. Tali interruzioni potrebbero comportare attività costose, ad esempio, la ricompilazione degli indici, o addirittura una perdita di replica. 

In molti altri casi, la disponibilità generale del servizio può essere migliorata eseguendo la normale sequenza di arresto, ad esempio il completamento o l'annullamento della transazioni in corso, la riassegnazione delle attività ad altre macchine virtuali nel cluster, ovvero il failover manuale, o la rimozione della macchina virtuale da un pool di bilanciamento del carico della rete. 

Ci sono casi in cui è possibile migliorare l'utilità delle applicazioni ospitate nel cloud avvisando un amministratore della presenza di un evento imminente o registrando tale evento.

Il Servizio metadati di Azure presenta gli eventi pianificati nei seguenti casi d'uso:
-   Manutenzione avviata dalla piattaforma, ad esempio implementazione del sistema operativo host
-   Chiamate avviate dall'utente, ad esempio riavvii iniziati dall'utente o ridistribuzione di una macchina virtuale


## <a name="the-basics"></a>Nozioni di base  

Il Servizio metadati di Azure presenta informazioni sulle macchine virtuali in esecuzione usando un endpoint REST accessibile dall'interno della macchina virtuale. Le informazioni sono disponibili tramite un indirizzo IP non instradabile, in modo da non essere esposte al di fuori della macchina virtuale.

### <a name="scope"></a>Scope
Gli eventi pianificati vengono presentati per tutte le macchine virtuali in un servizio cloud o per tutte le macchine virtuali in un set di disponibilità. Pertanto, è consigliabile controllare il campo `Resources` nell'evento per individuare le macchine virtuali che ne saranno interessate. 

### <a name="discovering-the-endpoint"></a>Individuazione dell'endpoint
Nel caso in cui venga creata una macchina virtuale in una rete virtuale, il servizio di metadati è disponibile all'indirizzo IP statico non instradabile, `169.254.169.254`.
Se la macchina virtuale non viene creata all'interno di una rete virtuale, i casi predefiniti per i servizi cloud e le macchine virtuali classiche, sarà necessaria una logica aggiuntiva per individuare l'endpoint da usare. Fare riferimento a questo esempio per informazioni su come [individuare l'endpoint dell'host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Controllo delle versioni 
Il Servizio metadati dell'istanza è con versione. Le versioni sono obbligatorie e la versione corrente è `2017-03-01`.

> [!NOTE] 
> Le versioni precedenti di anteprima di eventi pianificati {ultima} sono supportate come versione dell'API. Questo formato non è più supportato e verrà rimosso in futuro.

### <a name="using-headers"></a>Uso delle intestazioni
Quando si eseguono query sul Servizio metadati, è necessario specificare l'intestazione `Metadata: true` per garantire che la richiesta non sia stata reindirizzata accidentalmente.

### <a name="enabling-scheduled-events"></a>Attivazione degli eventi pianificati
La prima volta che si emette una richiesta per gli eventi pianificati, Azure abilita in modo implicito la funzionalità nella macchina virtuale. Di conseguenza la prima chiamata potrebbe ricevere una risposta con un ritardo massimo di due minuti.

### <a name="user-initiated-maintenance"></a>Manutenzione avviata dall'utente
La manutenzione delle macchine virtuali avviata dall'utente tramite il portale, l'API o l'interfaccia della riga di comando di Azure oppure tramite PowerShell restituisce un evento pianificato. In questo modo è possibile testare la logica di preparazione della manutenzione dell'applicazione e permettere all'applicazione di prepararsi per la manutenzione avviata dall'utente.

Il riavvio di una macchina virtuale pianifica un evento di tipo `Reboot`. La ridistribuzione di una macchina virtuale pianifica un evento di tipo `Redeploy`.

> [!NOTE] 
> Attualmente è possibile pianificare un massimo di 10 operazioni di manutenzione avviata dall'utente. Questo limite viene aumentato prima della disponibilità generale di eventi pianificati.

> [!NOTE] 
> Attualmente la manutenzione avviata dall'utente che dà luogo a eventi pianificati non è configurabile. Questa funzionalità è pianificata per una versione successiva.

## <a name="using-the-api"></a>Uso dell'API

### <a name="query-for-events"></a>Query per gli eventi
È possibile eseguire query per gli eventi pianificati semplicemente eseguendo la chiamata seguente:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
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

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Il riconoscimento di un evento consente all'evento di procedere per tutti gli elementi `Resources` nell'evento, non solo per la macchina virtuale che riconosce l'evento. Pertanto è possibile scegliere un coordinatore che si occupi del riconoscimento, che potrebbe essere semplicemente il primo nel campo `Resources`.




## <a name="python-sample"></a>Esempio Python 

L'esempio seguente esegue query sul servizio metadati per gli eventi pianificati e quindi approva gli eventi in sospeso.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Passaggi successivi 

- Altre informazioni sulle API disponibili nel [servizio metadati dell'istanza](instance-metadata-service.md).
- Informazioni sulla [manutenzione pianificata per le macchine virtuali Linux in Azure](planned-maintenance.md).

