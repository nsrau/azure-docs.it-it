---
title: Limiti di Azure Data Box Gateway | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per Microsoft Azure Data Box Gateway.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: edb4995b626055be830a7accb74d99f1db3ef8d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962202"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Limiti di Azure Data Box Gateway (anteprima)


Quando si distribuisce e si usa la soluzione Microsoft Azure Data Box Gateway, tenere presenti i limiti seguenti. 

> [!IMPORTANT] 
> Data Box Gateway è in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-gateway-service-limits"></a>Limiti del servizio Data Box Gateway

- In questa versione, il servizio è disponibile solo in determinate aree geografiche di Stati Uniti, Europa e Asia Pacifico. Per altre informazioni, vedere [Disponibilità a livello di area](#data-box-gateway-overview#region-availability). L'account di archiviazione deve essere fisicamente più vicino all'area in cui è distribuito il dispositivo (può essere diverso dalle impostazioni geografiche del servizio).
- Non è supportato lo spostamento di una risorsa di Data Box Gateway in un'altra sottoscrizione o in un altro gruppo di risorse. Per altri dettagli, vedere [Spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Limiti del dispositivo Data Box Gateway

La tabella seguente illustra i limiti del dispositivo Data Box Gateway.

| DESCRIZIONE | Valore |
|---|---|
|No. di file per dispositivo |100 milioni <br> Il limite è di circa 25 milioni di file per ogni 2 TB di spazio su disco con un limite massimo di 100 milioni |
|No. di condivisioni per dispositivo |24 |
|Dimensione massima dei file scritti in una condivisione|5 TB |

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

Questa sezione descrive i limiti del servizio Archiviazione di Azure e le convenzione di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabili al servizio Data Box Gateway/Data Box Edge. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

> [!IMPORTANT]
> Se sono presenti file o directory che superano i limiti del servizio Archiviazione di Azure o che non sono conformi alle convenzioni di denominazione di BLOB o File di Azure, tali file o directory non vengono inseriti in Archiviazione di Azure tramite il servizio Data Box Gateway/Data Box Edge.

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, il dispositivo sovrascriverà il file nel cloud. 
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.


## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni più aggiornate su questi limiti, vedere [Obiettivi di scalabilità dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Obiettivi di scalabilità di File di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB di pagine e BLOB in blocchi                                            | 500 TB per account di archiviazione|


## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | ~ 8 TB                                                 |
| BLOB di pagine         | 1 TB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
