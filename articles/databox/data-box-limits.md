---
title: Limiti di Azure Data Box | Microsoft Docs
description: Vengono descritti i limiti di sistema e le dimensioni consigliate per componenti e connessioni di Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/01/2020
ms.author: alkohli
ms.openlocfilehash: b193247fee843796da0cb2fcfc1a874b1a1b72fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660922"
---
# <a name="azure-data-box-limits"></a>Limiti di Azure Data Box

Quando si distribuisce e si usa Microsoft Azure Data Box, tenere presenti i limiti seguenti. La tabella seguente illustra i limiti di Data Box.

## <a name="data-box-service-limits"></a>Limiti di servizio Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Limiti di Data Box

- Data Box possibile archiviare un massimo di 500 milioni file per l'importazione e l'esportazione.
- Data Box supporta un massimo di 512 contenitori o condivisioni nel cloud. Le directory di livello superiore all'interno della condivisione utente diventano contenitori o condivisioni file di Azure nel cloud. 
- Data Box capacità di utilizzo può essere inferiore a 80 TB a causa del consumo dello spazio dei metadati ReFS.
- Data Box supporta un massimo di 5 connessioni client alla volta in una condivisione NFS. 

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati


### <a name="for-import-order"></a>Per ordine di importazione

Data Box Avvertenze per un ordine di importazione includono:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Per l'ordine di esportazione

Data Box Avvertenze per un ordine di esportazione includono:

- Data Box è un dispositivo basato su Windows e non supporta nomi di file con distinzione tra maiuscole e minuscole. Ad esempio, è possibile avere due file diversi in Azure con nomi che si differenziano solo per la combinazione di maiuscole e minuscole. Non usare Data Box per esportare tali file perché i file verranno sovrascritti nel dispositivo.
- Se sono presenti tag duplicati nei file di input o nei tag che fanno riferimento agli stessi dati, l'esportazione Data Box potrebbe ignorare o sovrascrivere i file. Il numero di file e le dimensioni dei dati visualizzati nel portale di Azure potrebbero differire dalle dimensioni effettive dei dati nel dispositivo. 
- Data Box Esporta i dati in un sistema basato su Windows tramite SMB ed è limitato da limitazioni SMB per file e cartelle. I file e le cartelle con nomi non supportati non vengono esportati.
- È presente un mapping 1:1 dal prefisso al contenitore.
- La dimensione massima del nome file è di 1024 caratteri. I nomi file che superano questa lunghezza non vengono esportati.
- I prefissi duplicati nel file *XML* (caricati durante la creazione dell'ordine) vengono esportati. I prefissi duplicati non vengono ignorati.
- I BLOB di pagine e i nomi dei contenitori fanno distinzione tra maiuscole e minuscole Se la combinazione di maiuscole e minuscole non corrisponde, il BLOB e/o il contenitore non verranno trovati.
 

## <a name="azure-storage-account-size-limits"></a>Limiti delle dimensioni dell'account di archiviazione di Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenzioni di denominazione per BLOB in blocchi, BLOB di pagine e file

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
