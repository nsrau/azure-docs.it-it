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
translationtype: Human Translation
ms.sourcegitcommit: bb4f7c4977de290e6e148bbb1ae8b28791360f96
ms.openlocfilehash: 1a385de3c00b9288d9e1245f04969a9099bf5b45
ms.lasthandoff: 03/01/2017


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
Il Servizio metadati usa un'API con controllo delle versioni nel seguente formato: http://{ip}/metadata/{version}/scheduledevents È consigliabile che il servizio usi la versione più recente disponibile all'indirizzo: http://{ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>Uso delle intestazioni
Quando si esegue una query al Servizio metadati, è necessario specificare l'intestazione seguente *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Abilitare gli eventi pianificati
La prima volta che vengono chiamati gli eventi pianificati, Azure abilita in modo implicito la funzionalità nella macchina virtuale. Di conseguenza la prima chiamata potrebbe ricevere una risposta con un ritardo massimo di due minuti.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Test della logica con le operazioni avviate dall'utente
Per testare la logica è possibile usare il Portale di Azure, l'API, l'interfaccia della riga di comando o PowerShell, in modo da avviare le operazioni che determinano gli eventi pianificati. Il riavvio di una macchina virtuale determina un evento pianificato con un tipo di evento uguale a Reboot. La ridistribuzione di una macchina virtuale determina un evento pianificato con un tipo di evento uguale a Redeploy.
In entrambi i casi l'operazione avviata dall'utente richiede più tempo per il completamento poiché gli eventi pianificati lasciano un tempo più lungo per la corretta chiusura di un'applicazione. 

## <a name="using-the-api"></a>Uso dell'API

### <a name="query-for-events"></a>Query per gli eventi
È possibile eseguire query per gli eventi pianificati semplicemente effettuando la chiamata seguente

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

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

EventType acquisisce l'impatto previsto nella macchina virtuale in cui:
- Freeze: è pianificata una sospensione della macchina virtuale per alcuni secondi. La memoria, i file aperti o le connessioni di rete non subiranno conseguenze
- Reboot: è pianificato un riavvio per la macchina virtuale. La memoria verrà svuotata.
- Redeploy: è pianificato uno spostamento della macchina virtuale in un altro nodo. I dischi temporanei andranno persi. 

Quando è pianificato un evento (stato = Scheduled), Azure condivide l'ora dopo la quale l'evento può iniziare, specificata nel campo NotBefore.

### <a name="starting-an-event-expedite"></a>Avvio di un evento (urgente)

Dopo aver appreso di un evento imminente e aver completato la logica per l'arresto normale, è possibile indicare ad Azure di accelerare, se possibile, eseguendo una chiamata **POST** 
 

## <a name="powershell-sample"></a>Esempio PowerShell 

Nell'esempio seguente il server di metadati viene letto alla ricerca di eventi pianificati che vengono registrati nel registro eventi dell'applicazione prima del riconoscimento.

```PowerShell
$localHostIP = "169.254.169.254"
$ScheduledEventURI = "http://"+$localHostIP+"/metadata/latest/scheduledevents"

# Call Azure Metadata Service - Scheduled Events 
$scheduledEventsResponse =  Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $ScheduledEventURI -Method get 

if ($json.Events.Count -eq 0 )
{
    Write-Output "++No scheduled events were found"
}

for ($eventIdx=0; $eventIdx -lt $scheduledEventsResponse.Events.Length ; $eventIdx++)
{
    if ($scheduledEventsResponse.Events[$eventIdx].Resources[0].ToLower().substring(1) -eq $env:COMPUTERNAME.ToLower())
    {    
        # YOUR LOGIC HERE 
         pause "This Virtual Machine is scheduled for to "+ $scheduledEventsResponse.Events[$eventIdx].EventType

        # Acknoledge the event to expedite
        $jsonResp = "{""StartRequests"" : [{ ""EventId"": """+$scheduledEventsResponse.events[$eventIdx].EventId +"""}]}"
        $respbody = convertto-JSon $jsonResp
       
        Invoke-RestMethod -Uri $ScheduledEventURI  -Headers @{"Metadata"="true"} -Method POST -Body $jsonResp 
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
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
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
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
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
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
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

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
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
[Manutenzione pianificata per macchine virtuali in Azure](./virtual-machines-linux-planned-maintenance.md)

