---
title: Note sulla versione di anteprima di Azure Data Box Gateway | Microsoft Docs
description: Descrive i problemi critici non ancora risolti e le soluzioni trovate per la versione di anteprima in esecuzione di Azure Data Box Gateway.
services: databox-edge-gateway
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 7619056ace5d9b3cf083a40a6cfa06a0cac0561e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949690"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Note sulla versione di anteprima di Azure Data Box Gateway

## <a name="overview"></a>Panoramica

Le note sulla versione seguenti identificano i problemi critici non ancora risolti e quelli risolti per la versione di anteprima di Microsoft Azure Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire Data Box Gateway, esaminare attentamente le informazioni contenute nelle note sulla versione.

La versione di anteprima corrisponde alla versione software **Data Box Gateway Preview Version 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problemi risolti nella versione di anteprima

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Problema |
| --- | --- |
| 1 | In questa versione, quando un file caricato da un altro strumento (AzCopy) viene aggiornato con un conseguente aumento/estensione delle dimensioni del file, viene osservato l'errore seguente: *Errore 400: InvalidBlobOrBlock (il blob o il contenuto del blocco non è valido.)*|


## <a name="known-issues-in-preview-release"></a>Problemi noti nella versione di anteprima

La tabella seguente contiene un riepilogo dei problemi noti per la versione di anteprima in esecuzione di Data Box Gateway.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Aggiornamenti |I dispositivi Data Box Gateway creati nelle versioni di anteprima precedenti non possono essere aggiornati a questa versione. |Scaricare le immagini dei dischi virtuali dalla nuova versione, quindi configurare e distribuire i nuovi dispositivi. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Disco dati sottoposto a provisioning |Dopo aver eseguito il provisioning di un disco dati di una determinata dimensione specificata e aver creato il corrispondente Data Box Gateway, non ridurre il disco dati. Il tentativo di ridurre il disco comporta la perdita di tutti i dati locali sul dispositivo. | |
| **3.** |Aggiorna |In questa versione è possibile aggiornare una sola condivisione alla volta. | |
| **4.** |Rinominare |Non è consentito rinominare gli oggetti. |Se questa funzionalità è fondamentale per il proprio flusso di lavoro, contattare il supporto tecnico Microsoft. |
| **5.** |Copia| Se un file di sola lettura viene copiato nel dispositivo, la proprietà di sola lettura non viene mantenuta. | |
| **6.** |Log| A causa di un bug in questa versione, in *error.xml* potrebbero verificarsi istanze del codice errore 110 con nomi di elementi non riconoscibili. | |
| **7.** |Caricamento | A causa di un bug in questa versione, potrebbero verificarsi istanze del codice errore 2003 durante il caricamento di file specifici. | |
| **8.** |Eliminazione | A causa di un bug in questa versione, potrebbe essere impossibile eliminare una condivisione NFS. Lo stato della condivisione rimarrà comunque *In fase di eliminazione*.  |Questo problema si verifica solo quando la condivisione usa un nome file non supportato. |
| **9.** |Guida online |I collegamenti della Guida nel portale di Azure potrebbero non condurre alla documentazione.|I collegamenti della Guida funzioneranno correttamente nella versione con disponibilità generale. |



## <a name="next-steps"></a>Passaggi successivi

- [Operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


