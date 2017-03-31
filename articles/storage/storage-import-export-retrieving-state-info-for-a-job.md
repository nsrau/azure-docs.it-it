---
title: Recupero di informazioni sullo stato per un processo di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come ottenere informazioni sullo stato per i processi del servizio Importazione/Esportazione di Microsoft Azure
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: d8156439cc81a88172d5af97c31147b6ceb23ff6
ms.lasthandoff: 02/16/2017


---

# <a name="retrieving-state-information-for-an-importexport-job"></a>Recupero delle informazioni sullo stato per un processo di Importazione/Esportazione di Azure
È possibile chiamare l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) per recuperare informazioni su entrambi i processi: importazione ed esportazione. Le informazioni restituite includono:

-   Lo stato corrente del processo.

-   La percentuale approssimativa di completamento di ogni processo.

-   Lo stato corrente di ogni unità.

-   Gli URI per i BLOB contenenti i log degli errori e informazioni dettagliate sulla registrazione (se abilitati).

Le sezioni seguenti illustrano le informazioni restituite dall'operazione `Get Job`.

## <a name="job-states"></a>Stati del processo
La tabella e il diagramma degli stati seguenti descrivono gli stati di un processo attraverso il ciclo di vita. Lo stato corrente del processo può essere determinato chiamando l'operazione `Get Job`.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

La tabella seguente descrive ogni stato in cui può trovarsi un processo.

|Stato del processo|Descrizione|
|---------------|-----------------|
|`Creating`|Dopo avere chiamato l'operazione Put Job, viene creato un processo e il relativo stato viene impostato su `Creating`. Mentre il processo si trova nello stato `Creating`, il servizio Importazione/Esportazione presuppone che le unità non siano state spedite al data center. Un processo può rimanere nello stato `Creating` fino a due settimane; al termine di questo periodo viene automaticamente eliminato dal servizio.<br /><br /> Se si chiama l'operazione Update Job Properties mentre il processo si trova nello stato `Creating`, il processo rimane nello stato `Creating` e l'intervallo di timeout viene reimpostato su due settimane.|
|`Shipping`|Dopo avere spedito il pacchetto, è necessario chiamare l'operazione Update Job Properties per aggiornare lo stato del processo in `Shipping`. Lo stato di spedizione può essere impostato solo se `DeliveryPackage` (spedizioniere e numero di tracciabilità) e le proprietà `ReturnAddress` sono state impostate per il processo.<br /><br /> Il processo rimarrà in questo stato per un massimo di due settimane. Se sono trascorse due settimane e le unità non sono state ricevute, gli operatori del servizio Importazione/Esportazione riceveranno una notifica.|
|`Received`|Dopo che tutte le unità sono state ricevute nel data center, il processo verrà impostato sullo stato ricevuto.|
|`Transferring`|Dopo che le unità sono state ricevute nel data center ed è iniziata l'elaborazione di almeno un'unità, il processo verrà impostato sullo stato `Transferring`. Vedere la sezione `Drive States` per informazioni dettagliate.|
|`Packaging`|Una volta completata l'elaborazione di tutte le unità, il processo verrà inserito nello stato `Packaging` fino a quando le unità non vengono spedite di nuovo al cliente.|
|`Completed`|Dopo che tutte le unità sono state rispedite al cliente, se il processo è stato completato senza errori, il processo verrà impostato sullo stato `Completed`. Il processo verrà eliminato automaticamente dopo 90 giorni in cui si trova nello stato `Completed`.|
|`Closed`|Dopo che tutte le unità sono state rispedite al cliente, se si sono verificati errori durante l'elaborazione del processo, il processo verrà impostato sullo stato `Closed`. Il processo verrà eliminato automaticamente dopo 90 giorni in cui si trova nello stato `Closed`.|

È possibile annullare un processo solo in determinati stati. Un processo annullato ignora il passaggio di copia dei dati, ma in caso contrario segue le stesse transizioni di stato di un processo non annullato.

La tabella seguente descrive gli errori che possono verificarsi per ogni stato del processo e l'impatto sul processo in caso di errore.

|Stato del processo|Evento|Risoluzione/passaggi successivi|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Sono state ricevute una o più unità per un processo, ma il processo non si trova nello stato `Shipping` oppure non esiste un record del processo nel servizio.|Il team operativo del servizio Importazione/Esportazione tenterà di contattare il cliente per creare o aggiornare il processo con le informazioni necessarie per far avanzare il processo.<br /><br /> Se il team non è in grado di contattare il cliente entro due settimane, tenterà di restituire le unità.<br /><br /> Nel caso in cui non sia possibile restituire le unità e il cliente non sia raggiungibile, le unità verranno distrutte in modo sicuro entro 90 giorni.<br /><br /> Si noti che un processo non può essere elaborato fino a quando il relativo stato non viene aggiornato a `Shipping`.|
|`Shipping`|Il pacchetto per un processo non è arrivato per più di due settimane.|Il team operativo informerà il cliente che manca il pacchetto. In base alla risposta del cliente, il team prorogherà l'intervallo di attesa del pacchetto in arrivo o annullerà il processo.<br /><br /> Nel caso in cui il cliente non possa essere contattato o non risponda entro 30 giorni, il team operativo avvierà un'azione per passare il processo dallo stato `Shipping` direttamente allo stato `Closed`.|
|`Completed/Closed`|Le unità non sono mai state ricevute all'indirizzo del mittente o sono state danneggiate durante la spedizione; si applica solo a un processo di esportazione.|Se le unità non raggiungono l'indirizzo del mittente, il cliente deve prima chiamare l'operazione Get Job o controllare lo stato del processo nel portale per verificare se le unità sono state spedite. Se le unità sono state spedite, il cliente deve contattare il provider della spedizione per tentare di individuare le unità.<br /><br /> Se le unità vengono danneggiate durante la spedizione, il cliente può richiedere un altro processo di esportazione o scaricare i BLOB mancanti.|
|`Transferring/Packaging`|L'indirizzo del mittente per il processo manca o è errato.|Il team operativo raggiungerà il contatto per il processo per ottenere l'indirizzo corretto.<br /><br /> Nel caso in cui il cliente non sia raggiungibile, le unità verranno distrutte in modo sicuro entro 90 giorni.|
|`Creating / Shipping/ Transferring`|Il pacchetto di spedizione include un'unità non presente nell'elenco di unità da importare.|Le unità aggiuntive non verranno elaborate e verranno restituite al cliente al completamento del processo.|

## <a name="drive-states"></a>Stati dell'unità
La tabella e diagramma di seguito descrivono il ciclo di vita di una singola unità attraverso un processo di importazione o esportazione. È possibile recuperare lo stato dell'unità corrente chiamando l'operazione `Get Job` ed esaminando l'elemento `State` della proprietà `DriveList`.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

La tabella seguente descrive ogni stato in cui un processo può trovarsi.

|Stato dell'unità|Descrizione|
|-----------------|-----------------|
|`Specified`|Per un processo di importazione, quando il processo viene creato con l'operazione Put Job, lo stato iniziale di un'unità è `Specified`. Per un processo di esportazione, lo stato iniziale dell'unità è `Received` perché non è stata specificata un'unità in fase di creazione del processo.|
|`Received`|Le unità passano allo stato `Received` quando l'operatore del servizio Importazione/Esportazione avrà elaborato le unità ricevute dallo spedizioniere per un processo di importazione. Per un processo di esportazione, lo stato iniziale dell'unità è `Received`.|
|`NeverReceived`|L'unità passerà allo stato `NeverReceived` quando arriverà il pacchetto per un processo, ma il pacchetto non contiene l'unità. Un'unità può inoltre passare a questo stato se sono trascorse due settimane da quando il servizio ha ricevuto le informazioni sulla spedizione, ma il pacchetto non è ancora stato ricevuto nel data center.|
|`Transferring`|Un'unità passerà allo stato `Transferring` quando il servizio inizierà a trasferire i dati dall'unità in Archiviazione di Windows Azure.|
|`Completed`|Un'unità passerà allo stato `Completed` quando il servizio avrà trasferito tutti i dati senza errori.|
|`CompletedMoreInfo`|Un'unità passerà allo stato `CompletedMoreInfo` quando il servizio ha riscontrato alcuni problemi durante la copia dei dati da o nell'unità. Le informazioni possono includere errori, avvisi o messaggi informativi sulla sovrascrittura dei BLOB.|
|`ShippedBack`|L'unità passerà allo stato `ShippedBack` quando verrà spedita dal data center di nuovo all'indirizzo del mittente.|

La tabella seguente descrive gli stati di errore delle unità e le azioni intraprese corrispondenti.

|Stato dell'unità|Evento|Risoluzione/passaggio successivo|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Un'unità contrassegnata come `NeverReceived` (perché non è stata ricevuta come parte della spedizione del processo) viene ricevuta con un'altra spedizione.|Il team operativo sposterà l'unità nello stato `Received`.|
|`N/A`|Un'unità non inclusa nel processo viene ricevuta nel data center come parte di un altro processo.|L'unità verrà contrassegnata come unità aggiuntiva e verrà restituita al cliente al completamento del processo associato al pacchetto originale.|

## <a name="faulted-states"></a>Stati con errori
Quando un processo o un'unità non procede regolarmente nel ciclo di vita previsto, il processo o l'unità verrà spostato in uno stato `Faulted`. A questo punto, il team operativo contatterà il cliente tramite posta elettronica o telefono. Una volta risolto il problema, il processo o l'unità con errore passerà dallo stato `Faulted` allo stato appropriato.

## <a name="see-also"></a>Vedere anche
[Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

