---
title: Eventi pianificati per macchine virtuali Linux in Azure | Microsoft Docs
description: Pianificare gli eventi utilizzando i metadati di servizio per le macchine virtuali Linux.
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
ms.openlocfilehash: ae9955253647f3277729e7905baf7bb07645de42
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure Metadata Service: Gli eventi pianificati (anteprima) per le macchine virtuali Linux

> [!NOTE] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Gli eventi pianificati è un servizio secondario nel servizio di metadati di Azure che fornisce il tempo applicazione per preparare per la manutenzione delle macchine virtuali (VM). Fornisce informazioni sugli eventi di manutenzione programmata (ad esempio, riavviare il computer) in modo che l'applicazione può preparare la transizione e limitare l'interruzione. È disponibile per tutti i tipi di macchine virtuali di Azure, tra cui PaaS e IaaS su Windows e Linux. 

Per informazioni su Eventi pianificati in Windows, vedere [Eventi pianificati per macchine virtuali Windows](../windows/scheduled-events.md).

## <a name="why-use-scheduled-events"></a>Perché utilizzare agli eventi pianificati?

Molte applicazioni possono trarre vantaggio dal tempo di preparazione per la manutenzione di macchina virtuale. L'ora può essere utilizzato per eseguire attività specifiche dell'applicazione che consentono di migliorare la disponibilità, affidabilità e gestibilità, tra cui: 

- Checkpoint e il ripristino.
- Svuotamento di connessione.
- Failover della replica primaria.
- Rimozione da un pool di bilanciamento del carico.
- Registrazione degli eventi.
- Arresto normale.

Con agli eventi pianificati, è possibile individuare l'applicazione quando manutenzione verrà e generano attività per limitare l'impatto.  

Gli eventi pianificati informano sugli eventi nei casi d'uso seguenti:

- Manutenzione avviata alla piattaforma (ad esempio, un aggiornamento del sistema operativo host)
- Manutenzione avviata dall'utente (ad esempio, un utente riavvia o ridistribuisce una macchina virtuale)

## <a name="the-basics"></a>Nozioni di base  

  Metadata Service espone informazioni sulle macchine virtuali in esecuzione con un endpoint REST che sarà accessibile dall'interno della macchina virtuale. Le informazioni sono disponibili tramite un indirizzo IP nonroutable in modo che non è esposta all'esterno della macchina virtuale.

### <a name="scope"></a>Scope
Gli eventi pianificati vengono recapitati a:

- Tutte le macchine virtuali in un servizio cloud.
- Tutte le macchine virtuali in un set di disponibilità.
- Tutte le macchine virtuali in una scala di impostare il gruppo di posizionamento. 

Di conseguenza, controllare il `Resources` campo nell'evento per identificare le macchine virtuali sono interessate.

### <a name="discover-the-endpoint"></a>Individuare l'endpoint
Per le macchine virtuali che sono abilitate per le reti virtuali, l'endpoint completo per la versione più recente di agli eventi pianificati è: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Nel caso in cui viene creata una macchina virtuale in una rete virtuale, è disponibile da un indirizzo IP statico nonroutable, Metadata Service `169.254.169.254`.
Se la macchina virtuale non è stata creata in una rete virtuale, ai casi predefiniti per i servizi cloud e le macchine virtuali classiche, sarà necessaria logica aggiuntiva per individuare l'indirizzo IP da utilizzare. Per informazioni su come [individuare l'endpoint dell'host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), vedere questo esempio.

### <a name="versioning"></a>Controllo delle versioni 
Il servizio agli eventi pianificati è con controllo delle versioni. Le versioni sono obbligatorie, e la versione corrente è `2017-08-01`.

| Version | Note sulla versione | 
| - | - | 
| 2017-08-01 | <li> È stato rimosso il carattere di sottolineatura all'inizio dei nomi delle risorse per le macchine virtuali IaaS<br><li>Requisito di intestazione dei metadati applicato per tutte le richieste | 
| 2017-03-01 | <li>Versione di anteprima pubblica


> [!NOTE] 
> Versioni precedenti di anteprima di eventi pianificati supportati {più recente} come la versione dell'api. Questo formato non è più supportato e verrà rimosso in futuro.

### <a name="use-headers"></a>Utilizzare le intestazioni
Quando si eseguono query Metadata Service, è necessario specificare l'intestazione `Metadata:true` affinché involontariamente non è stata reindirizzata la richiesta. L'intestazione `Metadata:true` è obbligatoria per tutte le richieste di eventi pianificati. Errore di includere l'intestazione nella richiesta di risultati in una risposta "Richiesta non valida" Metadata Service.

### <a name="enable-scheduled-events"></a>Abilitare gli eventi pianificati
La prima volta che si effettua una richiesta per gli eventi pianificati, in modo implicito e consente la funzionalità di una macchina virtuale. Di conseguenza, attende una risposta ritardata nella prima chiamata di fino a due minuti.

> [!NOTE]
> Gli eventi pianificati viene disabilitata automaticamente per il servizio se il servizio non chiama l'endpoint per un giorno. Dopo aver disabilitato agli eventi pianificati per il servizio, gli eventi non vengono creati per la manutenzione avviata dall'utente.

### <a name="user-initiated-maintenance"></a>Manutenzione avviata dall'utente
Manutenzione di macchina virtuale avviata dall'utente tramite il portale di Azure, API, CLI o PowerShell genera un evento pianificato. È quindi possibile testare la logica di preparazione di manutenzione dell'applicazione e l'applicazione è possibile preparare per la manutenzione avviata dall'utente.

Se si riavvia una macchina virtuale, un evento con il tipo `Reboot` è pianificata. Se si ridistribuisce una macchina virtuale, un evento con il tipo `Redeploy` è pianificata.

> [!NOTE] 
> Attualmente, un massimo di 100 operazioni di manutenzione avviata dall'utente può essere pianificato contemporaneamente.

> [!NOTE] 
> Attualmente, manutenzione avviata dall'utente che restituisce gli eventi pianificati non è configurabile. Questa funzionalità è pianificata per una versione successiva.

## <a name="use-the-api"></a>Usare l'API

### <a name="query-for-events"></a>Query per gli eventi
È possibile eseguire una query per gli eventi pianificati, eseguendo la chiamata seguente:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Una risposta contiene una serie di eventi pianificati. Matrice vuota significa che attualmente gli eventi non pianificati.
Nel caso in cui sono presenti eventi pianificati, la risposta contiene una matrice di eventi. 
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
|Proprietà  |  DESCRIZIONE |
| - | - |
| EventId | Identificatore globalmente univoco per l'evento. <br><br> Esempio: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impatto che l'evento causa. <br><br> Valori: <br><ul><li> `Freeze`: La macchina virtuale è pianificata per mettere in pausa per pochi secondi. La CPU è sospesa, ma non è presente alcun effetto sulla memoria, i file aperti o connessioni di rete. <li>`Reboot`: La macchina virtuale è pianificata per il riavvio. (Memoria non persistente viene persa). <li>`Redeploy`: La macchina virtuale è pianificata per spostare in un altro nodo. (Dischi temporanei andranno persi). |
| ResourceType | Tipo di risorsa che influisce su questo evento. <br><br> Valori: <ul><li>`VirtualMachine`|
| Risorse| Elenco delle risorse, influisce su questo evento. L'elenco è garantito per contenere le macchine da al massimo un [dominio di aggiornamento](manage-availability.md), ma potrebbe non contenere tutti i computer il UD. <br><br> Esempio: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stato dell'evento. <br><br> Valori: <ul><li>`Scheduled`: l'avvio dell'evento è pianificato in seguito al tempo specificato nella proprietà `NotBefore`.<li>`Started`: l'evento si è avviato.</ul> Non `Completed` o simile stato mai disponibile. L'evento non viene restituito al termine dell'evento.
| NotBefore| Tempo trascorso il quale è possibile avviare questo evento. <br><br> Esempio: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Pianificazione degli eventi
Ogni evento è pianificata una quantità minima di tempo in futuro in base al tipo di evento. Questo tempo si riflette in una proprietà `NotBefore` dell'evento. 

|EventType  | Si noti minimo |
| - | - |
| Freeze| 15 minuti |
| Reboot | 15 minuti |
| Ripetere la distribuzione | 10 minuti |

### <a name="start-an-event"></a>Un evento di avvio 

Dopo aver conoscenza di un evento futuro e completare la logica per l'arresto normale, è possibile approvare l'evento in sospeso eseguendo una `POST` chiamare a Metadata Service con `EventId`. Questa chiamata indica a Azure che consente di ridurre la notifica minima di tempo (sempre). 

In cui è previsto il seguente esempio JSON di `POST` corpo della richiesta. La richiesta deve contenere un elenco di `StartRequests`. Ogni `StartRequest` contiene `EventId` per l'evento che si desidera accelerare:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Esempio di barra rovesciata
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Consente di riconoscimento di un evento dell'evento di procedere per tutti i `Resources` nel caso in cui, non solo la macchina virtuale che riconosce l'evento. Pertanto, è possibile scegliere di scegliere se un carattere di riempimento per coordinare il riconoscimento, che potrebbe essere semplice come la prima macchina nel `Resources` campo.

## <a name="python-sample"></a>Esempio Python 

L'esempio seguente esegue una query Metadata Service per gli eventi pianificati e approva ogni evento in sospeso:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
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
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare gli esempi di codice agli eventi pianificati nel [repository Github per gli eventi pianificati di Azure istanza metadati](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Altre informazioni sulle API disponibili nel [servizio metadati dell'istanza](instance-metadata-service.md).
- Informazioni sulla [manutenzione pianificata per le macchine virtuali Linux in Azure](planned-maintenance.md).
