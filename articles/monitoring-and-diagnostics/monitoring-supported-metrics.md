<properties
    pageTitle="Metriche di monitoraggio di Azure: metriche supportate per tipo di risorsa | Microsoft Azure"
    description="Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>


# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure.

> [AZURE.NOTE] Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. Questo elenco include solo le metriche di anteprima pubblica disponibili tramite l'anteprima pubblica della pipeline della metrica di monitoraggio di Azure consolidata.

## <a name="microsoft.batch/batchaccounts"></a>Microsoft.Batch/batchAccounts

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

## <a name="microsoft.cache/redis"></a>Microsoft.Cache/redis

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
|totalkeys0|Totale chiavi (nodo 0)|Numero|Massima||
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
|totalkeys1|Totale chiavi (nodo 1)|Numero|Massima||
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
|totalkeys2|Totale chiavi (nodo 2)|Numero|Massima||
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
|totalkeys3|Totale chiavi (nodo 3)|Numero|Massima||
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
|totalkeys4|Totale chiavi (nodo 4)|Numero|Massima||
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
|totalkeys5|Totale chiavi (nodo 5)|Numero|Massima||
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
|totalkeys6|Totale chiavi (nodo 6)|Numero|Massima||
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
|totalkeys7|Totale chiavi (nodo 7)|Numero|Massima||
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
|totalkeys8|Totale chiavi (nodo 8)|Numero|Massima||
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
|totalkeys9|Totale chiavi (nodo 9)|Numero|Massima||
|expiredkeys9|Chiavi scadute (partizione 9)|Numero|Totale||
|usedmemory9|Memoria usata (partizione 9)|Byte|Massima||
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massima||
|serverLoad9|Carico server (partizione 9)|Percentuale|Massima||
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massima||
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massima||
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massima||

## <a name="microsoft.cognitiveservices/accounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|NumberOfCalls|Numero totale di chiamate API|Conteggio|Totale|Numero totale di chiamate API.|
|NumberOfFailedCalls|Numero totale di chiamate API non riuscite|Conteggio|Totale|Numero totale di chiamate API non riuscite.|

## <a name="microsoft.compute/virtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoft.compute/virtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoft.compute/virtualmachinescalesets/virtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|

## <a name="microsoft.devices/iothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi telemetria|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|
|d2c.telemetry.ingress.success|Messaggi telemetria inviati|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|
|c2d.commands.egress.complete.success|Comandi completati|Numero|Totale|Il numero di comandi da cloud a dispositivo completati dal dispositivo|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Numero|Totale|Il numero di comandi da cloud a dispositivo abbandonati dal dispositivo|
|c2d.commands.egress.reject.success|Comandi rifiutati|Numero|Totale|Il numero di comandi da cloud a dispositivo rifiutati dal dispositivo|
|devices.totalDevices|Dispositivi totali|Numero|Totale|Il numero di dispositivi registrati nell'hub IoT|
|devices.connectedDevices.allProtocol|Dispositivi connessi|Numero|Totale|Il numero di dispositivi connessi all'hub IoT|

## <a name="microsoft.eventhub/namespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|INREQS|Richieste in ingresso|Numero|Totale|Velocità effettiva dei messaggi in ingresso di Hub eventi per uno spazio dei nomi|
|SUCCREQ|Richieste riuscite|Numero|Totale|Richieste riuscite totali per uno spazio dei nomi|
|FAILREQ|Richieste non riuscite|Numero|Totale|Richieste non riuscite totali per uno spazio dei nomi|
|SVRBSY|Errori server occupato|Numero|Totale|Errori di server occupato totali per uno spazio dei nomi|
|INTERR|Errori interni server|Numero|Totale|Errori di server interni totali per uno spazio dei nomi|
|MISCERR|Altri errori|Numero|Totale|Richieste non riuscite totali per uno spazio dei nomi|
|INMSGS|Messaggi in ingresso|Numero|Totale|Messaggi in ingresso totali per uno spazio dei nomi|
|OUTMSGS|Messaggi in uscita|Numero|Totale|Messaggi in uscita totali per uno spazio dei nomi|
|EHINMBS|Byte in ingresso al secondo|Byte al secondo|Totale|Velocità effettiva dei messaggi in ingresso di Hub eventi per uno spazio dei nomi|
|EHOUTMBS|Byte in uscita al secondo|Byte al secondo|Totale|Messaggi in uscita totali per uno spazio dei nomi|
|EHABL|Messaggi backlog archiviati|Numero|Totale|Messaggi archiviati di Hub eventi nel backlog per uno spazio dei nomi|
|EHAMSGS|Messaggi archiviati|Numero|Totale|Messaggi archiviati di Hub eventi in uno spazio dei nomi|
|EHAMBS|Velocità effettiva messaggi archiviati|Byte al secondo|Totale|Velocità effettiva dei messaggi archiviati di Hub eventi in uno spazio dei nomi|

## <a name="microsoft.logic/workflows"></a>Microsoft.Logic/workflows

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

## <a name="microsoft.network/applicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media||

## <a name="microsoft.search/searchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|

## <a name="microsoft.servicebus/namespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPUXNS|Uso della CPU per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo CPU per spazio dei nomi Premium del bus di servizio|
|WSXNS|Uso delle dimensioni della memoria per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo memoria per spazio dei nomi Premium del bus di servizio|

## <a name="microsoft.sql/servers/databases"></a>Microsoft.Sql/servers/databases

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
|xtp_storage_percent|Percentuale archiviazione OLTP interna alla memoria (anteprima)|Percentuale|Media|Percentuale archiviazione OLTP interna alla memoria (anteprima)|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|
|service_level_objective|Obiettivo del livello di servizio del database|Numero|Totale|Obiettivo del livello di servizio del database|
|dwu_limit|Limite DWU|Numero|Massima|Limite DWU|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Media|Percentuale DWU|
|dwu_used|Uso DWU|Conteggio|Media|Uso DWU|

## <a name="microsoft.sql/servers/elasticpools"></a>Microsoft.Sql/servers/elasticPools

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

## <a name="microsoft.streamanalytics/streamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

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

## <a name="microsoft.web/serverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|
|DiskQueueLength|Lunghezza coda disco|Numero|Totale|Lunghezza coda disco|
|HttpQueueLength|Lunghezza coda HTTP|Numero|Totale|Lunghezza coda HTTP|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|

## <a name="microsoft.web/sites"></a>Microsoft.Web/sites

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Requests|Requests|Numero|Totale|Requests|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|
|Http401|Http 401|Numero|Totale|Http 401|
|Http403|Http 403|Numero|Totale|Http 403|
|Http404|Http 404|Numero|Totale|Http 404|
|Http406|Http 406|Numero|Totale|Http 406|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|
|MemoryWorkingSet|Working set della memoria|Byte|Totale|Working set della memoria|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|

## <a name="microsoft.web/sites/slots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Requests|Requests|Numero|Totale|Requests|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|
|Http401|Http 401|Numero|Totale|Http 401|
|Http403|Http 403|Numero|Totale|Http 403|
|Http404|Http 404|Numero|Totale|Http 404|
|Http406|Http 406|Numero|Totale|Http 406|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|
|MemoryWorkingSet|Working set della memoria|Byte|Totale|Working set della memoria|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|

## <a name="next-steps"></a>Passaggi successivi

- [Metriche in Azure Monitor](./monitoring-overview.md#monitoring-sources)
- [Create alerts on metrics](./insights-receive-alert-notifications.md)
- [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](./monitoring-overview-of-diagnostic-logs.md)



<!--HONumber=Oct16_HO2-->


