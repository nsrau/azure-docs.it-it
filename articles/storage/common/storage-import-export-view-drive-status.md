---
title: Visualizzare lo stato dei processi di Importazione/Esportazione di Azure | Microsoft Docs
description: Informazioni su come visualizzare lo stato dei processi di Importazione/Esportazione e delle unità usate.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478046"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Visualizzare lo stato dei processi di Importazione/Esportazione di Azure

Questo articolo fornisce informazioni su come visualizzare lo stato delle unità e dei processi per i processi di Importazione/Esportazione di Azure. Il servizio Importazione/Esportazione di Azure viene usato per il trasferimento sicuro grandi quantità di dati in BLOB di Azure e File di Azure. Il servizio viene usato anche per esportare i dati da Archiviazione BLOB di Azure.  

## <a name="view-job-and-drive-status"></a>Visualizzare lo stato di processi e unità
È possibile tenere traccia dello stato dei processo di importazione o esportazione dal portale di Azure. Fare clic sulla scheda **Importazione/Esportazione**. Nella pagina viene visualizzato un elenco dei processi.

![Visualizzare lo stato dei processi](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Visualizzare lo stato di un processo

Viene visualizzato uno degli stati di processo seguenti, in base al punto in cui si trova l'unità nel processo.

| Stato processo | DESCRIZIONE |
|:--- |:--- |
| Creating | Dopo la creazione di un processo, lo stato è impostato su **Creazione**. Mentre il processo si trova nello stato **Creazione**, il servizio Importazione/Esportazione presuppone che le unità non siano state spedite al data center. Un processo può rimanere in questo stato per un massimo di due settimane; trascorse le quali viene automaticamente eliminato dal servizio. |
| Spedizione | Dopo aver spedito il pacchetto, è consigliabile aggiornare le informazioni di tracciabilità nel portale di Azure.  Lo stato del processo viene modificato in **Spedizione**. Il processo rimane nello stato **Spedizione** per un massimo di due settimane. 
| Ricevuto | Dopo che tutte le unità sono state ricevute nel data center, lo stato del processo viene impostato su **Ricevuto**. |
| Transferring | Dopo l'avvio dell'elaborazione per almeno un'unità, lo stato del processo viene impostato su **Trasferimento**. Per altre informazioni, vedere [Visualizzare lo stato dell'unità](#view-drive-status). |
| Packaging | Una volta completata l'elaborazione di tutte le unità, lo stato del processo viene impostato su **Imballaggio** fino a quando le unità non vengono rispedite all'utente. |
| Completi | Dopo che tutte le unità sono state rispedite, se il processo è stato completato senza errori, il suo stato viene impostato su **Completato**. Il processo viene eliminato automaticamente dopo 90 giorni in cui si trova nello stato **Completato**. |
| Chiuso | Dopo che tutte le unità sono state rispedite, se si sono verificati errori durante l'elaborazione del processo, il suo stato viene impostato su **Chiuso**. Il processo viene eliminato automaticamente dopo 90 giorni in cui si trova nello stato **Chiuso**. |

## <a name="view-drive-status"></a>Visualizzare lo stato dell'unità

La tabella di seguito descrive il ciclo di vita di una singola unità attraverso un processo di importazione o esportazione. Lo stato corrente di ogni unità in un processo è visualizzato nel portale di Azure.

La tabella seguente descrive ogni stato in cui può trovarsi un'unità in un processo.

| Stato dell'unità | DESCRIZIONE |
|:--- |:--- |
| Specificata | Per un processo di importazione, quando il processo viene creato dal portale di Azure, lo stato iniziale di un'unità è **Specificata**. Per un processo di esportazione, lo stato iniziale dell'unità è **Ricevuta**, perché non è stata specificata alcuna unità al momento della creazione del processo. |
| Ricevuto | Le unità passano allo stato **Ricevuta** quando il servizio Importazione/Esportazione ha elaborato le unità ricevute dallo spedizioniere per un processo di importazione. Per un processo di esportazione, lo stato iniziale dell'unità è **Ricevuta**. |
| MaiRicevuta | L'unità passa allo stato **MaiRicevuta** se il pacchetto di un processo viene ricevuto, ma non contiene l'unità. Un'unità può anche passare a questo stato se sono trascorse due settimane da quando il servizio ha ricevuto le informazioni sulla spedizione, ma il pacchetto non è ancora stato ricevuto nel data center. |
| Transferring | Un'unità passa allo stato **Trasferimento** quando il servizio inizia a trasferire i dati dall'unità ad Archiviazione di Azure. |
| Completi | Un'unità passa allo stato **Completata** se il servizio ha trasferito correttamente tutti i dati senza errori.
| CompletataPiùInformazioni | Un'unità passa allo stato **CompletataPiùInformazioni** se il servizio ha riscontrato problemi durante la copia dei dati da o verso l'unità. Le informazioni possono includere errori, avvisi o messaggi informativi sulla sovrascrittura dei BLOB.
| Rispedita | Un'unità passa allo stato **Rispedita** quando viene spedita dal data center all'indirizzo mittente. |

Questa immagine dal portale di Azure consente di visualizzare lo stato dell'unità di un processo di esempio:

![Visualizza stato dell'unità](./media/storage-import-export-service/drivestate.png)

La tabella seguente descrive gli stati di errore delle unità e le azioni intraprese corrispondenti.

| Stato dell'unità | Event | Risoluzione/Passaggio successivo |
|:--- |:--- |:--- |
| MaiRicevuta | Un'unità contrassegnata come **MaiRicevuta** (perché non è stata ricevuta come parte della spedizione del processo) viene ricevuta con un'altra spedizione. | Il team delle operazioni modifica lo stato in **Ricevuta**. |
| N/D | Un'unità che non fa parte di alcun processo arriva al data center come parte di un altro processo. | L'unità viene contrassegnata come unità aggiuntiva e viene restituita al cliente una volta completato il processo associato al pacchetto originale. |

## <a name="time-to-process-job"></a>Tempo di elaborazione del processo
La quantità di tempo necessario per elaborare un processo di importazione/esportazione varia in base a diversi fattori, tra cui:

-  Ora di spedizione
-  Carico nel data center
-  Tipo di processo e dimensioni dei dati da copiare
-  Numero di dischi in un processo 

Il servizio di importazione/esportazione non dispone di un contratto di servizio, ma tenta di completare la copia in un intervallo compreso tra i 7 e i 10 giorni dopo la ricezione dei dischi. Oltre allo stato pubblicato nel portale di Azure, è possibile usare le API REST per tenere traccia dell'avanzamento del processo. Il parametro relativo alla percentuale di completamento nella chiamata API all'operazione di [elenco dei processi](/previous-versions/azure/dn529083(v=azure.100)) indica l'avanzamento della percentuale di copia.


## <a name="next-steps"></a>Passaggi successivi

* [Configurare lo strumento WAImportExport](storage-import-export-tool-how-to.md)
* [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md)
* [Esempio di API REST del servizio Importazione/Esportazione di Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
