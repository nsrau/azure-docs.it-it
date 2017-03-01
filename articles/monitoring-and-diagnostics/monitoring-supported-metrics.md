---
title: 'Metriche di Monitoraggio di Azure: metriche supportate per tipo di risorsa | Microsoft Docs'
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/17/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 354bf45625c209c22118804d3835ca71e3128580
ms.openlocfilehash: deda64fb779e176bb00c3256fa3028e7e3567eb4
ms.lasthandoff: 02/18/2017


---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure
Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure.

> [!NOTE]
> Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. Questo elenco include solo le metriche di anteprima pubblica disponibili tramite l'anteprima pubblica della pipeline della metrica di monitoraggio di Azure consolidata.
> 
> 

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|qpu_metric|QPU|Numero|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|
|TotalConnectionRequests|Numero totale di richieste di connessione|Numero|Media|Numero totale delle richieste di connessione in arrivo.|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|
|TotalConnectionFailures|Numero totale di errori di connessione|Numero|Media|Numero totale dei tentativi di connessione non riusciti.|
|CurrentUserSessions|Sessioni utente correnti|Numero|Media|Numero corrente di sessioni utente attive.|
|QueryPoolBusyThreads|Thread occupati pool di query|Numero|Media|Numero dei thread occupati nel pool dei thread di query.|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Numero|Media|Numero dei processi nella coda del pool dei thread dei comandi.|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Numero|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|TotalRequests|Totale richieste gateway|Numero|Totale|Numero di richieste del gateway|
|SuccessfulRequests|Richieste gateway riuscite|Numero|Totale|Numero di richieste del gateway riuscite|
|UnauthorizedRequests|Richieste del gateway non autorizzate|Numero|Totale|Numero di richieste del gateway non autorizzate|
|FailedRequests|Richieste gateway non riuscite|Numero|Totale|Numero di errori nelle richieste gateway|
|OtherRequests|Altre richieste del gateway|Numero|Totale|Numero di altre richieste del gateway|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CoreCount|Numero di core|Conteggio|Totale|Numero totale di core nell'account Batch|
|TotalNodeCount|Numero di nodi|Conteggio|Totale|Numero totale di nodi nell'account Batch|
|CreatingNodeCount|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|
|StartingNodeCount|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|
|IdleNodeCount|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|
|OfflineNodeCount|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|
|RebootingNodeCount|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|
|RunningNodeCount|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|
|UnusableNodeCount|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|
|TaskStartEvent|Eventi attività avviate|Numero|Totale|Il numero totale di attività avviate|
|TaskCompleteEvent|Eventi attività completate|Numero|Totale|Il numero totale di attività completate|
|TaskFailEvent|Eventi attività non riuscite|Numero|Totale|Il numero totale di attività completate con lo stato Non riuscito|
|PoolCreateEvent|Eventi pool creati|Numero|Totale|Il numero totale di pool che sono stati creati|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Numero|Totale|Il numero totale di ridimensionamenti dei pool avviati|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Numero|Totale|Il numero totale di ridimensionamenti dei pool completati|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Numero|Totale|Il numero totale di eliminazioni di pool avviate|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Numero|Totale|Il numero totale di eliminazioni di pool completate|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|connectedclients|Client connessi|Numero|Massima||
|totalcommandsprocessed|Totale operazioni|Numero|Totale||
|cachehits|Riscontri cache|Numero|Totale||
|cachemisses|Mancati riscontri nella cache|Numero|Totale||
|getcommands|Operazioni Get|Numero|Totale||
|setcommands|Operazioni Set|Numero|Totale||
|evictedkeys|Chiavi rimosse|Numero|Totale||
|totalkeys|Totale chiavi|Numero|Massima||
|expiredkeys|Chiavi scadute|Numero|Totale||
|usedmemory|Memoria utilizzata|Byte|Massima||
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massima||
|serverLoad|Carico server|Percentuale|Massima||
|cacheWrite|Scrittura nella cache|Byte al secondo|Massima||
|cacheRead|Lettura da cache|Byte al secondo|Massima||
|percentProcessorTime|CPU|Percentuale|Massima||
|connectedclients0|Client connessi (partizione 0)|Numero|Massima||
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Numero|Totale||
|cachehits0|Riscontri cache (partizione 0)|Numero|Totale||
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Numero|Totale||
|getcommands0|Operazioni Get (partizione 0)|Numero|Totale||
|setcommands0|Operazioni Set (partizione 0)|Numero|Totale||
|evictedkeys0|Chiavi rimosse (partizione 0)|Numero|Totale||
|totalkeys0|Totale chiavi (partizione 0)|Numero|Massima||
|expiredkeys0|Chiavi scadute (partizione 0)|Numero|Totale||
|usedmemory0|Memoria usata (partizione 0)|Byte|Massima||
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massima||
|serverLoad0|Carico server (partizione 0)|Percentuale|Massima||
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massima||
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massima||
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massima||
|connectedclients1|Client connessi (partizione 1)|Numero|Massima||
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Numero|Totale||
|cachehits1|Riscontri cache (partizione 1)|Numero|Totale||
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Numero|Totale||
|getcommands1|Operazioni Get (partizione 1)|Numero|Totale||
|setcommands1|Operazioni Set (partizione 1)|Numero|Totale||
|evictedkeys1|Chiavi rimosse (partizione 1)|Numero|Totale||
|totalkeys1|Totale chiavi (partizione 1)|Numero|Massima||
|expiredkeys1|Chiavi scadute (partizione 1)|Numero|Totale||
|usedmemory1|Memoria usata (partizione 1)|Byte|Massima||
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massima||
|serverLoad1|Carico server (partizione 1)|Percentuale|Massima||
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massima||
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massima||
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massima||
|connectedclients2|Client connessi (partizione 2)|Numero|Massima||
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Numero|Totale||
|cachehits2|Riscontri cache (partizione 2)|Numero|Totale||
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Numero|Totale||
|getcommands2|Operazioni Get (partizione 2)|Numero|Totale||
|setcommands2|Operazioni Set (partizione 2)|Numero|Totale||
|evictedkeys2|Chiavi rimosse (partizione 2)|Numero|Totale||
|totalkeys2|Totale chiavi (partizione 2)|Numero|Massima||
|expiredkeys2|Chiavi scadute (partizione 2)|Numero|Totale||
|usedmemory2|Memoria usata (partizione 2)|Byte|Massima||
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massima||
|serverLoad2|Carico server (partizione 2)|Percentuale|Massima||
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massima||
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massima||
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massima||
|connectedclients3|Client connessi (partizione 3)|Numero|Massima||
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Numero|Totale||
|cachehits3|Riscontri cache (partizione 3)|Numero|Totale||
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Numero|Totale||
|getcommands3|Operazioni Get (partizione 3)|Numero|Totale||
|setcommands3|Operazioni Set (partizione 3)|Numero|Totale||
|evictedkeys3|Chiavi rimosse (partizione 3)|Numero|Totale||
|totalkeys3|Totale chiavi (partizione 3)|Numero|Massima||
|expiredkeys3|Chiavi scadute (partizione 3)|Numero|Totale||
|usedmemory3|Memoria usata (partizione 3)|Byte|Massima||
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massima||
|serverLoad3|Carico server (partizione 3)|Percentuale|Massima||
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massima||
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massima||
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massima||
|connectedclients4|Client connessi (partizione 4)|Numero|Massima||
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Numero|Totale||
|cachehits4|Riscontri cache (partizione 4)|Numero|Totale||
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Numero|Totale||
|getcommands4|Operazioni Get (partizione 4)|Numero|Totale||
|setcommands4|Operazioni Set (partizione 4)|Numero|Totale||
|evictedkeys4|Chiavi rimosse (partizione 4)|Numero|Totale||
|totalkeys4|Totale chiavi (partizione 4)|Numero|Massima||
|expiredkeys4|Chiavi scadute (partizione 4)|Numero|Totale||
|usedmemory4|Memoria usata (partizione 4)|Byte|Massima||
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massima||
|serverLoad4|Carico server (partizione 4)|Percentuale|Massima||
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massima||
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massima||
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massima||
|connectedclients5|Client connessi (partizione 5)|Numero|Massima||
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Numero|Totale||
|cachehits5|Riscontri cache (partizione 5)|Numero|Totale||
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Numero|Totale||
|getcommands5|Operazioni Get (partizione 5)|Numero|Totale||
|setcommands5|Operazioni Set (partizione 5)|Numero|Totale||
|evictedkeys5|Chiavi rimosse (partizione 5)|Numero|Totale||
|totalkeys5|Totale chiavi (partizione 5)|Numero|Massima||
|expiredkeys5|Chiavi scadute (partizione 5)|Numero|Totale||
|usedmemory5|Memoria usata (partizione 5)|Byte|Massima||
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massima||
|serverLoad5|Carico server (partizione 5)|Percentuale|Massima||
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massima||
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massima||
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massima||
|connectedclients6|Client connessi (partizione 6)|Numero|Massima||
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Numero|Totale||
|cachehits6|Riscontri cache (partizione 6)|Numero|Totale||
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Numero|Totale||
|getcommands6|Operazioni Get (partizione 6)|Numero|Totale||
|setcommands6|Operazioni Set (partizione 6)|Numero|Totale||
|evictedkeys6|Chiavi rimosse (partizione 6)|Numero|Totale||
|totalkeys6|Totale chiavi (partizione 6)|Numero|Massima||
|expiredkeys6|Chiavi scadute (partizione 6)|Numero|Totale||
|usedmemory6|Memoria usata (partizione 6)|Byte|Massima||
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massima||
|serverLoad6|Carico server (partizione 6)|Percentuale|Massima||
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massima||
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massima||
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massima||
|connectedclients7|Client connessi (partizione 7)|Numero|Massima||
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Numero|Totale||
|cachehits7|Riscontri cache (partizione 7)|Numero|Totale||
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Numero|Totale||
|getcommands7|Operazioni Get (partizione 7)|Numero|Totale||
|setcommands7|Operazioni Set (partizione 7)|Numero|Totale||
|evictedkeys7|Chiavi rimosse (partizione 7)|Numero|Totale||
|totalkeys7|Totale chiavi (partizione 7)|Numero|Massima||
|expiredkeys7|Chiavi scadute (partizione 7)|Numero|Totale||
|usedmemory7|Memoria usata (partizione 7)|Byte|Massima||
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massima||
|serverLoad7|Carico server (partizione 7)|Percentuale|Massima||
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massima||
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massima||
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massima||
|connectedclients8|Client connessi (partizione 8)|Numero|Massima||
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Numero|Totale||
|cachehits8|Riscontri cache (partizione 8)|Numero|Totale||
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Numero|Totale||
|getcommands8|Operazioni Get (partizione 8)|Numero|Totale||
|setcommands8|Operazioni Set (partizione 8)|Numero|Totale||
|evictedkeys8|Chiavi rimosse (partizione 8)|Numero|Totale||
|totalkeys8|Totale chiavi (partizione 8)|Numero|Massima||
|expiredkeys8|Chiavi scadute (partizione 8)|Numero|Totale||
|usedmemory8|Memoria usata (partizione 8)|Byte|Massima||
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massima||
|serverLoad8|Carico server (partizione 8)|Percentuale|Massima||
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massima||
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massima||
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massima||
|connectedclients9|Client connessi (partizione 9)|Numero|Massima||
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Numero|Totale||
|cachehits9|Riscontri cache (partizione 9)|Numero|Totale||
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Numero|Totale||
|getcommands9|Operazioni Get (partizione 9)|Numero|Totale||
|setcommands9|Operazioni Set (partizione 9)|Numero|Totale||
|evictedkeys9|Chiavi rimosse (partizione 9)|Numero|Totale||
|totalkeys9|Totale chiavi (partizione 9)|Numero|Massima||
|expiredkeys9|Chiavi scadute (partizione 9)|Numero|Totale||
|usedmemory9|Memoria usata (partizione 9)|Byte|Massima||
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massima||
|serverLoad9|Carico server (partizione 9)|Percentuale|Massima||
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massima||
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massima||
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massima||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|TotalCalls|Totale chiamate|Numero|Totale|Numero totale di chiamate.|
|SuccessfulCalls|Chiamate riuscite|Numero|Totale|Numero di chiamate riuscite.|
|TotalErrors|Totale errori|Numero|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|
|BlockedCalls|Chiamate bloccate|Numero|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|
|ServerErrors|Errori server|Numero|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|
|ClientErrors|Errori client|Numero|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|
|DataIn|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|
|DataOut|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|
|Latency|Latency|Millisecondi|Media|Latenza in millisecondi.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|
|c2d.commands.egress.complete.success|Comandi completati|Numero|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Numero|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|
|c2d.commands.egress.reject.success|Comandi rifiutati|Numero|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|
|devices.totalDevices|Totale dispositivi|Numero|Totale|Il numero di dispositivi registrati nell'hub IoT|
|devices.connectedDevices.allProtocol|Dispositivi connessi|Numero|Totale|Il numero di dispositivi connessi all'hub IoT|
|d2c.telemetry.egress.success|Messaggi telemetria recapitati|Numero|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint (totale)|
|d2c.telemetry.egress.dropped|Messaggi rimossi|Numero|Totale|Numero di messaggi rimossi perché non corrispondenti alle route e la route di fallback è stata disabilitata|
|d2c.telemetry.egress.orphaned|Messaggi orfani|Numero|Totale|Il numero di messaggi non corrispondenti ad alcuna route inclusa la route di fallback|
|d2c.telemetry.egress.invalid|Messaggi non validi|Numero|Totale|Il numero dei messaggi non recapitati per incompatibilità con l'endpoint|
|d2c.telemetry.egress.fallback|Messaggi corrispondenti alla condizione di fallback|Numero|Totale|Numero di messaggi scritti nell'endpoint di fallback|
|d2c.endpoints.egress.eventHubs|Messaggi recapitati agli endpoint dell'hub eventi|Numero|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'hub eventi|
|d2c.endpoints.latency.eventHubs|Latenza messaggi per gli endpoint di Hub eventi|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di Hub eventi, in millisecondi|
|d2c.endpoints.egress.serviceBusQueues|Messaggi recapitati agli endpoint della coda del bus di servizio|Numero|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint della coda del bus di servizio|
|d2c.endpoints.latency.serviceBusQueues|Latenza messaggi per gli endpoint della coda del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint della coda del bus di servizio, in millisecondi|
|d2c.endpoints.egress.serviceBusTopics|Messaggi recapitati agli endpoint dell'argomento del bus di servizio|Numero|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'argomento del bus di servizio|
|d2c.endpoints.latency.serviceBusTopics|Latenza messaggi per gli endpoint dell'argomento del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio, in millisecondi|
|d2c.endpoints.egress.builtIn.events|Messaggi recapitati all'endpoint predefinito (messaggi/eventi)|Numero|Totale|Numero di volte in cui i messaggi sono stati scritti nell'endpoint predefinito (messaggi/eventi)|
|d2c.endpoints.latency.builtIn.events|Latenza dei messaggi per l'endpoint predefinito (messaggi/eventi)|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi), in millisecondi |
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Numero|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Numero|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|
|twinQueries.success|Query dei dispositivi gemelli completate|Numero|Totale|Numero di tutte le query dei dispositivi gemelli completate.|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Numero|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Numero|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Numero|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Numero|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Numero|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Numero|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Numero|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Numero|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Numero|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|
|jobs.queryJobs.success|Query sui processi riuscite|Numero|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|
|jobs.queryJobs.failure|Query sui processi non riuscite|Numero|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|
|jobs.completed|Processi completati|Numero|Totale|Numero di tutti i processi completati.|
|jobs.failed|Processi non riusciti|Numero|Totale|Numero di tutti i processi non riusciti.|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|INREQS|Richieste in ingresso|Numero|Totale|Numero totale di richieste in ingresso per uno spazio dei nomi|
|SUCCREQ|Richieste riuscite|Numero|Totale|Richieste riuscite totali per uno spazio dei nomi|
|FAILREQ|Richieste non riuscite|Numero|Totale|Richieste non riuscite totali per uno spazio dei nomi|
|SVRBSY|Errori server occupato|Numero|Totale|Errori di server occupato totali per uno spazio dei nomi|
|INTERR|Errori interni server|Numero|Totale|Errori di server interni totali per uno spazio dei nomi|
|MISCERR|Altri errori|Numero|Totale|Richieste non riuscite totali per uno spazio dei nomi|
|INMSGS|Messaggi in ingresso|Numero|Totale|Messaggi in ingresso totali per uno spazio dei nomi|
|OUTMSGS|Messaggi in uscita|Numero|Totale|Messaggi in uscita totali per uno spazio dei nomi|
|EHINMBS|Byte in ingresso|Byte al secondo|Totale|Velocità effettiva dei messaggi in ingresso di Hub eventi per uno spazio dei nomi|
|EHOUTMBS|Byte in uscita|Byte al secondo|Totale|Messaggi in uscita totali per uno spazio dei nomi|
|EHABL|Messaggi backlog archiviati|Numero|Totale|Messaggi archiviati di Hub eventi nel backlog per uno spazio dei nomi|
|EHAMSGS|Messaggi archiviati|Numero|Totale|Messaggi archiviati di Hub eventi in uno spazio dei nomi|
|EHAMBS|Velocità effettiva messaggi archiviati|Byte al secondo|Totale|Velocità effettiva dei messaggi archiviati di Hub eventi in uno spazio dei nomi|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|
|RunsCompleted|Esecuzioni completate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro completate.|
|RunsSucceeded|Esecuzioni riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|
|RunsFailed|Esecuzioni non riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|
|RunsCancelled|Esecuzioni annullate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|
|RunThrottledEvents|Eventi limitati esecuzione|Numero|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|
|ActionsStarted|Azioni avviate |Numero|Totale|Il numero di azioni del flusso di lavoro avviate.|
|ActionsCompleted|Azioni completate |Numero|Totale|Il numero di azioni del flusso di lavoro completate.|
|ActionsSucceeded|Azioni riuscite |Numero|Totale|Il numero di azioni del flusso di lavoro riuscite.|
|ActionsFailed|Azioni non riuscite|Numero|Totale|Il numero di azioni del flusso di lavoro non riuscite.|
|ActionsSkipped|Azioni ignorate |Numero|Totale|Il numero di azioni del flusso di lavoro ignorate.|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|
|ActionThrottledEvents|Eventi limitati azione|Numero|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|
|TriggersStarted|Trigger avviati |Numero|Totale|Il numero di trigger del flusso di lavoro avviati.|
|TriggersCompleted|Trigger completati |Numero|Totale|Il numero di trigger del flusso di lavoro completati.|
|TriggersSucceeded|Trigger riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro riusciti.|
|TriggersFailed|Trigger non riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro non riusciti.|
|TriggersSkipped|Trigger ignorati|Numero|Totale|Il numero di trigger del flusso di lavoro ignorati.|
|TriggersFired|Trigger rimossi |Numero|Totale|Il numero di trigger del flusso di lavoro rimossi.|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|
|TriggerThrottledEvents|Eventi limitati trigger|Numero|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media||

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|registration.all|Operazione di registrazione|Numero|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |
|registration.create|Operazioni di creazione registrazione|Numero|Totale|Numero di tutte le creazioni di registrazioni riuscite.|
|registration.update|Operazioni di aggiornamento registrazione|Numero|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|
|registration.get|Operazioni di lettura registrazione|Numero|Totale|Numero di tutte le query su registrazioni riuscite.|
|registration.delete|Operazioni di eliminazione registrazione|Numero|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|
|incoming|Messaggi in ingresso|Numero|Totale|Numero di tutte le chiamate all'API di invio riuscite. |
|incoming.scheduled|Notifiche push pianificate inviate|Numero|Totale|Notifiche push pianificate annullate|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Numero|Totale|Notifiche push pianificate annullate|
|scheduled.pending|Notifiche pianificate in sospeso|Numero|Totale|Notifiche pianificate in sospeso|
|installation.all|Operazioni di gestione installazione|Numero|Totale|Operazioni di gestione installazione|
|installation.get|Ottieni operazioni di installazione|Numero|Totale|Ottieni operazioni di installazione|
|installation.upsert|Crea o aggiorna operazioni di installazione|Numero|Totale|Crea o aggiorna operazioni di installazione|
|installation.patch|Operazioni di installazione patch|Numero|Totale|Operazioni di installazione patch|
|installation.delete|Elimina operazioni di installazione|Numero|Totale|Elimina operazioni di installazione|
|outgoing.allpns.success|Notifiche riuscite|Numero|Totale|Numero di tutte le notifiche riuscite.|
|outgoing.allpns.invalidpayload|Errori payload|Numero|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Numero|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|
|outgoing.allpns.channelerror|Errori canale|Numero|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Numero|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|
|outgoing.wns.success|Notifiche WNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|
|outgoing.wns.badchannel|Errore canale WNS non valido|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Numero|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|
|outgoing.wns.throttled|Notifiche WNS limitate|Numero|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Numero|Totale|Windows Live non è raggiungibile.|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Numero|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Numero|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Numero|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Numero|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|
|outgoing.wns.channelthrottled|Canale WNS limitato|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|
|outgoing.wns.dropped|Notifiche WNS eliminate|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|
|outgoing.wns.pnserror|Errori WNS|Numero|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Numero|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|
|outgoing.apns.success|Notifiche APNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|
|outgoing.apns.badchannel|Errore canale APNS non valido|Numero|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Numero|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Numero|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|
|outgoing.apns.pnserror|Errori APNS|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|
|outgoing.gcm.success|Notifiche GCM completate|Numero|Totale|Numero di tutte le notifiche riuscite.|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|
|outgoing.gcm.throttled|Notifiche GCM limitate|Numero|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Numero|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Numero|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|
|outgoing.gcm.pnserror|Errori GCM|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|
|outgoing.mpns.success|Notifiche MPNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Numero|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Numero|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Numero|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|
|outgoing.mpns.pnserror|Errori MPNS|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPUXNS|Uso della CPU per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo CPU per spazio dei nomi Premium del bus di servizio|
|WSXNS|Uso delle dimensioni della memoria per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo memoria per spazio dei nomi Premium del bus di servizio|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|
|storage|Dimensioni totali database|Byte|Massima|Dimensioni totali database|
|connection_successful|Connessioni riuscite|Numero|Totale|Connessioni riuscite|
|connection_failed|Connessioni non riuscite|Numero|Totale|Connessioni non riuscite|
|blocked_by_firewall|Blocco da parte del firewall|Numero|Totale|Blocco da parte del firewall|
|deadlock|Deadlock|Numero|Totale|Deadlock|
|storage_percent|Percentuale di dimensioni del database|Percentuale|Massima|Percentuale di dimensioni del database|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|
|dtu_limit|Limite DTU|Numero|Media|Limite DTU|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|
|service_level_objective|Obiettivo del livello di servizio del database|Numero|Totale|Obiettivo del livello di servizio del database|
|dwu_limit|Limite DWU|Numero|Massima|Limite DWU|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Media|Percentuale DWU|
|dwu_used|Uso DWU|Conteggio|Media|Uso DWU|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|
|eDTU_limit|Limite eDTU|Conteggio|Media|Limite eDTU|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|
|eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massima|% utilizzo unità di streaming|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|
|Errori|Errori di runtime|Conteggio|Totale|Errori di runtime|
|DroppedOrAdjustedEvents|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|
|AMLCalloutRequests|Richieste di funzioni|Conteggio|Totale|Richieste di funzioni|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste di funzioni non riuscite|
|AMLCalloutInputEvents|Eventi di funzioni|Conteggio|Totale|Eventi di funzioni|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|
|DiskQueueLength|Lunghezza coda disco|Numero|Totale|Lunghezza coda disco|
|HttpQueueLength|Lunghezza coda HTTP|Numero|Totale|Lunghezza coda HTTP|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|

## <a name="microsoftwebsites-including-functions"></a>Microsoft.Web/sites (incluso Funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Requests|Requests|Numero|Totale|Requests|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|
|Http101|Http 101|Numero|Totale|Http 101|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|
|Http401|Http 401|Numero|Totale|Http 401|
|Http403|Http 403|Numero|Totale|Http 403|
|Http404|Http 404|Numero|Totale|Http 404|
|Http406|Http 406|Numero|Totale|Http 406|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Numero|Media|Unità di esecuzione della funzione|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Numero|Media|Conteggio delle esecuzioni della funzione|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Requests|Requests|Numero|Totale|Requests|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|
|Http101|Http 101|Numero|Totale|Http 101|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|
|Http401|Http 401|Numero|Totale|Http 401|
|Http403|Http 403|Numero|Totale|Http 403|
|Http404|Http 404|Numero|Totale|Http 404|
|Http406|Http 406|Numero|Totale|Http 406|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Numero|Media|Unità di esecuzione della funzione|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Numero|Media|Conteggio delle esecuzioni della funzione|

## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Azure Monitor](monitoring-overview.md#monitoring-sources)
* [Create alerts on metrics](insights-receive-alert-notifications.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](monitoring-overview-of-diagnostic-logs.md)


