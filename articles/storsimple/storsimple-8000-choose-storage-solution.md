---
title: Opzioni per il trasferimento di dati in Azure tramite un'appliance | Microsoft Docs
description: Informazioni su come scegliere l'appliance più adatta per trasferire i dati in Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/01/2018
ms.author: alkohli
ms.openlocfilehash: 0cb1a0bccbb989506988f36c515d59cddb832265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263396"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Confrontare le opzioni di trasferimento dei dati StorSimple, Sincronizzazione file di Azure e Data Box Edge 
 
Questo documento offre una panoramica delle opzioni per il trasferimento in Azure dei dati locali, mettendo a confronto Data Box Edge, Sincronizzazione file di Azure e StorSimple serie 8000.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview.md)**: Data Box Edge è un dispositivo di rete locale che sposta i dati da e verso Azure e dispone di una funzionalità di calcolo Edge per l'intelligenza artificiale per eseguire l'analisi preliminare dei dati durante il caricamento. Questo dispositivo è stato presentato a Ignite 2018 ed è disponibile in anteprima pubblica. Data Box Gateway è una versione virtuale del dispositivo con le stesse capacità di trasferimento dati.
- **[Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide.md)**: il servizio Sincronizzazione file di Azure può essere usato per centralizzare le condivisioni di file dell'organizzazione in File di Azure, mantenendo al contempo la flessibilità, le prestazioni e la compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. La disponibilità generale di Sincronizzazione file di Azure è stata annunciata all'inizio del 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview.md)**: StorSimple è un dispositivo ibrido che aiuta le aziende a consolidare la propria infrastruttura di archiviazione per l'archiviazione primaria, la protezione dei dati, l'archiviazione e il ripristino di emergenza in una singola soluzione attraverso una stretta integrazione con Archiviazione di Azure. Informazioni sul ciclo di vita del prodotto per StorSimple sono disponibili [qui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Riepilogo del confronto

|                           |StorSimple 8000   |Sincronizzazione file di Azure   |Data Box Edge (Anteprima)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Panoramica         |Risorsa di archiviazione ibrida in livelli e archiviazione|Archiviazione generale su file server con cloud a livelli e sincronizzazione multisito.  |Soluzione di archiviazione per eseguire l'analisi preliminare dei dati e inviarli ad Azure attraverso la rete.        |
|Scenari        |File server, archiviazione, destinazione di backup |File server, archiviazione (multisito)   |Trasferimento di dati, pre-elaborazione dei dati, inclusi inferenza ML, IoT, archiviazione    |
|Calcolo Edge     |Non disponibile |Non disponibile |Supporta l'esecuzione di contenitori usando Azure IoT Edge    |
|Fattore di forma      |Dispositivo fisico   |Agente installato in Windows Server |Dispositivo fisico   |
|Hardware         |Dispositivo fisico offerto da Microsoft insieme al servizio | Fornito dal cliente |Dispositivo fisico offerto da Microsoft insieme al servizio  |
|Formato dati      |Formato personalizzato   |File         |BLOB o File    |
|Supporto dei protocolli |iSCSI          |SMB, NFS    | SMB o NFS      |
|Prezzi          |[Prezzi di StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Prezzi di AFS](https://azure.microsoft.com/pricing/details/storage/files/)  |[Prezzi per Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview.md) e [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview.md)
- Informazioni su [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide.md)