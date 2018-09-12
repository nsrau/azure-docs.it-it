---
title: Limiti di Azure Data Box Disk | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per Microsoft Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/04/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 3b26c5f9a6d75725fb102d89192e77988b295dea
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782026"
---
# <a name="azure-data-box-disk-limits-preview"></a>Limiti di Azure Data Box Disk (anteprima)


Quando si distribuisce e si usa la soluzione Microsoft Azure Data Box Disk, tenere presenti i limiti seguenti. 

> [!IMPORTANT] 
> Azure Data Box Disk è disponibile in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-service-limits"></a>Limiti di servizio Data Box

 - Il servizio Data Box è disponibile solo negli Stati Uniti, nell'Unione Europea, in Canada e in Australia in tutte le aree di Azure per il cloud pubblico di Azure.
 - Con Data Box Disk è supportato un unico account di archiviazione.

## <a name="data-box-disk-performance"></a>Prestazioni di Data Box Disk

Nei test con dischi connessi tramite USB 3.0 sono risultate prestazioni dei dischi fino a 430 MB/s. I valori effettivi variano a seconda delle dimensioni dei file usate. Per i file più piccoli, le prestazioni potrebbero essere inferiori.

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

Questa sezione descrive i limiti relativi al servizio Archiviazione di Azure e le convenzioni di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabili al servizio Data Box. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

> [!IMPORTANT]
> Se sono presenti file o directory che superano i limiti del servizio Archiviazione di Azure o che non sono conformi alle convenzioni di denominazione di BLOB o File di Azure, tali file o directory non vengono inseriti in Archiviazione di Azure tramite il servizio Data Box.

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

- Non copiare i dati direttamente nei dischi. Copiare i dati in cartelle *BlockBlob* e *PageBlob* create in precedenza.
- Una cartella in *BlockBlob* e *PageBlob* è un contenitore. I contenitori, ad esempio, vengono creati come *BlockBlob/container* e *PageBlob/container*.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB) nel cloud con lo stesso nome dell'oggetto copiato, Data Box Disk sovrascriverà il file nel cloud.
- Ogni file scritto nelle condivisioni *BlockBlob* e *PageBlob* viene caricato come BLOB in blocchi e BLOB di pagine rispettivamente.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle *BlockBlob* e *PageBlob* non viene caricata.
- Se si verificano errori durante il caricamento dei dati in Azure, viene creato un log degli errori nell'account di archiviazione di destinazione. Il percorso del log degli errori è disponibile nel portale quando il caricamento è completo ed è possibile esaminare il log per eseguire azioni correttive. Non eliminare i dati dall'origine senza verificare i dati caricati.

## <a name="azure-storage-account-size-limits"></a>Limiti delle dimensioni dell'account di archiviazione di Azure

Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni più aggiornate su questi limiti, vedere [Obiettivi di scalabilità dell'archiviazione BLOB di Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Obiettivi di scalabilità di File di Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB di pagine e BLOB in blocchi                                            | 500 TB per account di archiviazione <br> Sono inclusi dati da tutte le origini, ad esempio Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | ~ 8 TB                                                 |
| BLOB di pagine         | 1 TB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Convenzioni di denominazione BLOB di pagine e BLOB in blocchi di Azure

| Entità                                       | Convenzioni                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomi di contenitori BLOB in blocchi e BLOB di pagine | Deve essere un nome DNS valido compreso tra 3 e 63 caratteri. <br>  Deve iniziare con una lettera o un numero. <br> Può contenere solo lettere minuscole, numeri e trattini (-). <br> Ogni trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. <br> I trattini consecutivi non sono consentiti nei nomi. |
| Nomi di BLOB per BLOB in blocchi e BLOB di pagine      | I nomi di BLOB fanno distinzione tra maiuscole e minuscole e possono contenere una qualsiasi combinazione di caratteri. <br> La lunghezza di un nome di BLOB deve essere compresa tra 1 e 1.024 caratteri. <br> I caratteri URL riservati devono essere preceduti da una sequenza di escape. <br>Il numero di segmenti del percorso che includono il nome BLOB non può essere superiore a 254. Un segmento di linea è la stringa tra caratteri di delimitatore consecutivi, ad esempio, la barra rovesciata '/', che corrisponde al nome di una directory virtuale. |
