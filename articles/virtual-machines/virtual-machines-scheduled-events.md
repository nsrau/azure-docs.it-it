---
title: Eventi pianificati con il Servizio metadati di Azure | Microsoft Docs
description: Intervenire in caso di eventi che interessano la macchina virtuale prima che si verifichino.
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
ms.date: 12/10/2016
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 627aa117ded0aaa519052d4ea1a1995ba2e363ee
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Servizio metadati di Azure - Eventi pianificati (anteprima)

> [!NOTE] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni vedere le [Condizioni Supplementari per l'Uso delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)
>

Gli eventi pianificati sono uno dei servizi secondari del Servizio metadati di Azure e presentano informazioni sugli eventi imminenti, ad esempio i riavvii, in modo che l'applicazione possa prepararsi di conseguenza e limitare le interruzioni. Il servizio è disponibile per tutti i tipi di macchine virtuali di Azure, inclusi PaaS e IaaS. Il servizio dà alla macchina virtuale il tempo sufficiente per eseguire attività preventive e ridurre al minimo l'effetto di un evento. 


## <a name="introduction---why-scheduled-events"></a>Introduzione: perché scegliere gli eventi pianificati?

Con gli eventi pianificati è possibile intervenire per limitare l'impatto sul servizio. I carichi di lavoro a più istanze, che utilizzano tecniche di replica per mantenere lo stato, potrebbero essere soggetti a frequenti interruzioni tra più istanze. Tali interruzioni potrebbero comportare attività costose, ad esempio, la ricompilazione degli indici, o addirittura una perdita di replica. In molti altri casi, l'uso di una sequenza di arresto normale migliora la disponibilità generale del servizio. Ad esempio, completare o annullare transazioni in corso, riassegnare altre attività ad altre macchine virtuali nel cluster (failover manuale), rimuovere la macchina virtuale da un pool di bilanciamento del carico. Ci sono casi in cui è possibile migliorare l'utilità delle applicazioni ospitate nel cloud avvisando un amministratore della presenza di un evento imminente o semplicemente registrando tale evento.
Il Servizio metadati di Azure presenta gli eventi pianificati nei seguenti casi d'uso:
-    Manutenzione avviata dalla piattaforma, ad esempio implementazione del sistema operativo host
-    Chiamate avviate dall'utente, ad esempio riavvii iniziati dall'utente o ridistribuzione di una macchina virtuale


## <a name="scheduled-events---the-basics"></a>Eventi pianificati: nozioni di base  

Il Servizio metadati di Azure presenta informazioni sulle macchine virtuali in esecuzione usando un endpoint REST all'interno della macchina virtuale. Le informazioni sono disponibili tramite un indirizzo IP non instradabile, in modo da non essere esposto al di fuori della macchina virtuale.

### <a name="scope"></a>Scope
Gli eventi pianificati vengono presentati per tutte le macchine virtuali in un servizio cloud o per tutte le macchine virtuali in un set di disponibilità. Pertanto, è consigliabile controllare il campo **Risorse** nell'evento per individuare le macchine virtuali che ne saranno interessate. 

### <a name="discover-the-endpoint"></a>Individuare l'endpoint
Nel caso in cui venga creata una macchina virtuale all'interno di una rete virtuale (VNet), il Servizio metadati è disponibile dall'indirizzo IP non instradabile 169.254.169.254. In caso contrario, nei casi predefiniti per i servizi cloud e le macchine virtuali classiche, sarà necessaria una logica aggiuntiva per individuare l'endpoint da usare. Fare riferimento a questo esempio per capire come [individuare l'endpoint dell'host] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>Controllo delle versioni 
Il Servizio metadati dell'istanza è con versione. Le versioni sono obbligatorie e la versione corrente è 2017-03-01.

> [!NOTE] 
> Le versioni precedenti di anteprima di eventi pianificati {ultima} sono supportate come versione dell'API. Questo formato non è più supportato e verrà rimosso in futuro.
>


### <a name="using-headers"></a>Uso delle intestazioni
Quando si esegue una query al Servizio metadati, è necessario specificare l'intestazione seguente *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Abilitare gli eventi pianificati
La prima volta che vengono chiamati gli eventi pianificati, Azure abilita in modo implicito la funzionalità nella macchina virtuale. Di conseguenza la prima chiamata potrebbe ricevere una risposta con un ritardo massimo di due minuti.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Test della logica con le operazioni avviate dall'utente
Per testare la logica è possibile usare il Portale di Azure, l'API, l'interfaccia della riga di comando o PowerShell, in modo da avviare le operazioni che determinano gli eventi pianificati. Il riavvio di una macchina virtuale determina un evento pianificato con un tipo di evento uguale a Reboot. La ridistribuzione di una macchina virtuale determina un evento pianificato con un tipo di evento uguale a Redeploy.
In entrambi i casi l'operazione avviata dall'utente richiede più tempo per il completamento poiché gli eventi pianificati lasciano un tempo più lungo per la corretta chiusura di un'applicazione. 

## <a name="using-the-api"></a>Uso dell'API

### <a name="query-for-events"></a>Query per gli eventi
È possibile eseguire query per gli eventi pianificati semplicemente eseguendo la chiamata seguente:

    curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01


Una risposta contiene una serie di eventi pianificati. Una serie vuota indica che al momento non sono presenti eventi pianificati.
Nel caso in cui siano presenti eventi pianificati, la risposta contiene una serie di eventi: 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }
    
### <a name="event-properties"></a>Proprietà dell'evento
|Proprietà  |  Descrizione |
| - | - |
| EventId |Identificatore globalmente univoco per l'evento. <br><br> Esempio: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impatto che l'evento causa. <br><br> Valori: <br><ul><li> <i>Freeze</i>: è pianificata una sospensione della macchina virtuale per alcuni secondi. La memoria, i file aperti o le connessioni di rete non subiranno conseguenze. <li> <i>Reboot</i>: è pianificato un riavvio per la macchina virtuale. La memoria verrà svuotata.<li> <i>Redeploy</i>: è pianificato uno spostamento della macchina virtuale in un altro nodo. I dischi temporanei andranno persi. |
| ResourceType | Tipo di risorsa su cui l'evento influisce. <br><br> Valori: <ul><li>VirtualMachine|
| Risorse| Elenco delle risorse su cui l'evento influisce. <br><br> Esempio: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Event Status | Stato dell'evento. <br><br> Valori: <ul><li><i>Scheduled</i>: l'evento è pianificato per iniziare dopo il tempo specificato nella proprietà <i>NotBefore</i>.<li><i>Started</i>: l'evento si è avviato.</i>
| NotBefore| Tempo dopo il quale l'evento può essere avviato. <br><br> Esempio: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Event Scheduling
Ogni evento è pianificato con un ritardo minimo che dipende dal tipo di evento. Questa volta si riflette in una proprietà <i>NotBefore</i> di un evento. 

|EventType  | Minimum Notice |
| - | - |
| Freeze| 15 minuti |
| Reboot | 15 minuti |
| Ripetere la distribuzione | 10 minuti |

### <a name="starting-an-event-expedite"></a>Avvio di un evento (urgente)

Dopo aver appreso di un evento imminente e aver completato la logica per l'arresto normale, è possibile indicare ad Azure di accelerare, se possibile, eseguendo una chiamata **POST** 
 

## <a name="powershell-sample"></a>Esempio PowerShell 

L'esempio seguente rileva gli eventi pianificati nel server di metadati e quindi approva gli eventi.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
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

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Esempio C\# 
L'esempio seguente si riferisce a un client che presenta le API per comunicare con il Servizio metadati
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
È possibile analizzare gli eventi pianificati usando le seguenti strutture di dati 

```csharp
    public class ScheduledEventsDocument
    {
        public string DocumentIncarnation;
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime? NotBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public string DocumentIncarnation;
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

Un programma di esempio che usa il client per recuperare, gestire e riconoscere gli eventi:   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
        {
            DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
        };
        
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Esempio Python 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>Passaggi successivi 
[Manutenzione pianificata per macchine virtuali in Azure](linux/planned-maintenance.md)
[Servizio metadati dell'istanza](virtual-machines-instancemetadataservice-overview.md)

