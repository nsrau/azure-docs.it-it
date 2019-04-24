---
title: Limiti di Azure Data Box | Microsoft Docs
description: Vengono descritti i limiti di sistema e le dimensioni consigliate per componenti e connessioni di Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326599"
---
# <a name="azure-data-box-limits"></a>Limiti di Azure Data Box

Quando si distribuisce e si usa Microsoft Azure Data Box, tenere presenti i limiti seguenti. La tabella seguente illustra i limiti di Data Box.


## <a name="data-box-service-limits"></a>Limiti di servizio Data Box

 - Se si usa più account di archiviazione con il servizio Data Box, tutti gli account di archiviazione devono appartenere alla stessa area di Azure.
 - È consigliabile usare non più di tre account di archiviazione. Usare più account di archiviazione potrebbe potenzialmente influire sulle prestazioni.

## <a name="data-box-limits"></a>Limiti di Data Box

- Data Box può archiviare un massimo di 500 milioni di file.

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

Questa sezione descrive i limiti relativi al servizio Archiviazione di Azure e le convenzioni di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabili al servizio Data Box. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

> [!IMPORTANT]
> Se sono presenti tutti i file o directory che superano i limiti del Servizio archiviazione di Azure, o non sono conformi alle convenzioni di denominazione di file/Blob di Azure, questi file o directory non vengono inserite in archiviazione di Azure tramite il servizio Data Box.

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

- Non copiare i file direttamente in uno qualsiasi di condivisioni creati in precedenza. È necessario creare una cartella sotto la condivisione e quindi copiare i file in tale cartella.
- Una cartella in *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* è un contenitore. I contenitori, ad esempio, vengono creati come *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Ogni cartella creata direttamente in *StorageAccount_AzureFiles* viene convertita in una condivisione file di Azure.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, Data Box sovrascriverà il file nel cloud.
- Ogni file scritto nelle condivisioni *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* viene caricato rispettivamente come BLOB in blocchi e BLOB di pagine.
- Archivio blob di Azure non supporta le directory. Se si crea una cartella nella cartella *StorageAccount_BlockBlob*, vengono create cartelle virtuali nel nome del BLOB. Per File di Azure, viene mantenuta la struttura di directory effettiva.
- Qualsiasi vuoto gerarchia di directory (senza tutti i file) creata nella *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* cartelle non è caricato.
- Se si verificano errori durante il caricamento dei dati in Azure, viene creato un log degli errori nell'account di archiviazione di destinazione. Il percorso del log degli errori è disponibile quando il caricamento è completo ed è possibile esaminare il log per eseguire azioni correttive. Non eliminare i dati dall'origine senza verificare i dati caricati.

## <a name="azure-storage-account-size-limits"></a>Limiti delle dimensioni dell'account di archiviazione di Azure

Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni più aggiornate su questi limiti, vedere [Obiettivi di scalabilità dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Obiettivi di scalabilità di File di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB in blocchi e BLOB di pagine                                            | 500 TiB per account di archiviazione. <br> Sono inclusi i dati da tutte le origini, tra cui Data Box.|
| File di Azure                                                          | 5 TiB per condivisione.<br> Tutte le cartelle sotto *StorageAccount_AzureFiles* devono rispettare questo limite.       |

## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | Circa 4,75 TiB                                                 |
| BLOB di pagine         | 8 TiB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |
| File di Azure        | 1 TiB                                                      |
| Dischi gestiti     | 4 TiB <br> Per altre informazioni sulle dimensioni e limiti, vedere: <li>[Obiettivi di scalabilità di SSDs Standard](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Obiettivi di scalabilità delle unità SSD Premium](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Obiettivi di scalabilità di dischi rigidi Standard](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Prezzi e fatturazione dei dischi gestiti](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenzioni di denominazione per BLOB in blocchi, BLOB di pagine e file

| Entità                                       | Convenzioni                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomi di contenitori BLOB in blocchi e BLOB di pagine | Deve essere un nome DNS valido compreso tra 3 e 63 caratteri. <br>  Deve iniziare con una lettera o un numero. <br> Può contenere solo lettere minuscole, numeri e trattini (-). <br> Ogni trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. <br> Nei nomi non sono consentiti trattini consecutivi. |
| Nomi delle condivisioni per i file di Azure                  | Vedere sopra.                                                                                                                                                                                                                                                                                                             |
| Nomi dei file e delle directory per i file di Azure     |<li> Mantengono le maiuscole/minuscole, non fanno distinzione tra maiuscole e minuscole e non devono superare i 255 caratteri. </li><li> Non possono terminare con la barra (/). </li><li>Se inserita, verrà automaticamente rimossa. </li><li> Non sono consentiti i caratteri seguenti: <code>" \\ / : \| < > * ?</code></li><li> I caratteri URL riservati devono essere preceduti da una sequenza di escape. </li><li> Non sono consentiti i caratteri non validi di percorso URL. Elementi di codice come \\uE000 non sono caratteri Unicode validi. Alcuni caratteri ASCII o Unicode, come caratteri di controllo (da 0x00 a 0x1F, \\u0081, e così via), non sono consentiti. Per le regole che controllano le stringhe Unicode in HTTP/1.1, vedere RFC 2616, sezione 2.2: Basic Rules e RFC 3987. </li><li> Non sono consentiti nomi di file seguenti: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, punto (.) e due punti (..).</li>|
| Nomi di BLOB per BLOB in blocchi e BLOB di pagine      | </li><li>I nomi di BLOB fanno distinzione tra maiuscole e minuscole e possono contenere una qualsiasi combinazione di caratteri. </li><li>La lunghezza di un nome di BLOB deve essere compresa tra 1 e 1.024 caratteri. </li><li>I caratteri URL riservati devono essere preceduti da una sequenza di escape. </li><li>Il numero di segmenti del percorso che includono il nome BLOB non può essere superiore a 254. Un segmento di linea è la stringa tra caratteri di delimitatore consecutivi, ad esempio, la barra rovesciata '/', che corrisponde al nome di una directory virtuale.</li> |
