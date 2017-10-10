---
title: Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure
description: Informazioni sui diversi modi di archiviare e accedere ai dati in Azure per aiutare a decidere quale tecnologia usare.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 192680cc3faee86c0a45bc9abe4b6579ec56f324
ms.contentlocale: it-it
ms.lasthandoff: 09/29/2017

---

# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure

Microsoft Azure offre molte funzionalità in Archiviazione di Azure per archiviare e accedere ai dati nel cloud. Questo articolo illustra le funzionalità File di Azure, BLOB di Azure e Dischi di Azure e aiuta a scegliere quella più adatta alle proprie esigenze.

## <a name="scenarios"></a>Scenari

La tabella seguente confronta File, BLOB e Dischi e illustra scenari di esempio appropriati per ognuna delle funzionalità.

| Funzionalità | Descrizione | Quando usare le autorizzazioni |
|--------------|-------------|-------------|
| **File di Azure** | Include un'interfaccia SMB, librerie client e un'[interfaccia REST](/rest/api/storageservices/file-service-rest-api) che consente l'accesso ai file archiviati ovunque ci si trovi. | Si intende aggiornare e spostare un'applicazione nel cloud che usa già le API del file system native per condividere i dati con altre applicazioni in esecuzione in Azure.<br/><br/>Si intende archiviare gli strumenti di sviluppo e di debug a cui deve essere possibile accedere da molte macchine virtuali. |
| **BLOB di Azure** | Include librerie client e un'[interfaccia REST](/rest/api/storageservices/blob-service-rest-api) che consente di archiviare i dati non strutturati e di accedervi su larga scala in BLOB in blocchi. | Si desidera che la propria applicazione supporti scenari di accesso casuale e tramite flusso.<br/><br/>Si desidera poter accedere ai dati dell'applicazione ovunque ci si trovi. |
| **Dischi di Azure** | Include librerie client e un'[interfaccia REST](/rest/api/compute/manageddisks/disks/disks-rest-api) che consente di archiviare in modo permanente i dati e di accedervi da un disco rigido virtuale collegato. | Si intende aggiornare e spostare le applicazioni che usano le API del file system native per leggere e scrivere dati su dischi permanenti.<br/><br/>Si intende archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco. |

## <a name="comparison-files-and-blobs"></a>Confronto: File e BLOB

La tabella seguente confronta File di Azure e BLOB di Azure.  
  
||||  
|-|-|-|  
|**Attributo**|**BLOB di Azure**|**File di Azure**|  
|Opzioni di durabilità|LRS, ZRS, GRS (e RA-GRS per una maggiore disponibilità)|LRS, GRS|  
|Accessibilità|API REST|API REST<br /><br /> SMB 2.1 e SMB 3.0 (API del file system standard)|  
|Connettività|API REST: tutto il mondo|API REST: tutto il mondo<br /><br /> SMB 2.1: nell'area<br /><br /> SMB 3.0: tutto il mondo|  
|Endpoint|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Directory|Spazio dei nomi flat|Oggetti directory veri|  
|Distinzione tra maiuscole e minuscole nei nomi|Fa distinzione tra maiuscole e minuscole.|Non fa distinzione tra maiuscole e minuscole, ma le mantiene così come sono|  
|Capacità|Contenitori fino a 500 TB|Condivisioni file da 5 TB|  
|Velocità effettiva|Fino a 60 MB/s per BLOB in blocchi|Fino a 60 MB/s per condivisione|  
|Dimensioni oggetto|Fino a 200 GB/BLOB in blocchi|Fino a 1 TB/file|  
|Capacità fatturata|In base ai byte scritti|In base alle dimensioni di file|  
|Librerie client|Supporto di più lingue|Supporto di più lingue|  
  
## <a name="comparison-files-and-disks"></a>Confronto: File e Dischi

File di Azure è un complemento di Dischi di Azure. Un disco può essere collegato a una sola macchina virtuale di Azure alla volta. I dischi sono dischi rigidi virtuali in formato fisso archiviati come BLOB di pagine in Archiviazione di Azure e vengono usati dalla macchina virtuale per archiviare dati durevoli. È possibile accedere alle condivisioni file in File di Azure nello stesso modo in cui si accede al disco locale (usando le API del file system native). Le condivisioni file possono essere condivise tra molte macchine virtuali.  
 
La tabella seguente confronta File di Azure e Dischi di Azure.  
 
||||  
|-|-|-|  
|**Attributo**|**Dischi di Azure**|**File di Azure**|  
|Scope|Esclusivo per una singola macchina virtuale|Accesso condiviso tra più macchine virtuali|  
|Snapshot e Copia|Sì|No|  
|Configurazione|Connesso all'avvio della macchina virtuale|Connesso dopo l'avvio della macchina virtuale|  
|Autenticazione|Predefinito|Impostato con comando net use|  
|Pulizia|Automatico|Manuale|  
|Accesso tramite REST|Non è possibile accedere ai file all'interno del disco rigido virtuale|È possibile accedere ai file archiviati in una condivisione|  
|Dimensioni massime|Disco di 1 TB|Condivisione file da 5 TB e file da 1 TB nella condivisione|  
|IOPS di 8 KB massimo|500 IOPS|1000 IOps|  
|Velocità effettiva|Fino a 60 MB/s per disco|Fino a 60 MB/s per condivisione file|  

## <a name="next-steps"></a>Passaggi successivi

Quando si decide la modalità di archiviazione e di accesso ai dati, è consigliabile valutare anche i costi coinvolti. Per altre informazioni, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Alcune funzionalità SMB non sono applicabili al cloud. Per altre informazioni, vedere [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Funzionalità non supportate da Servizio file di Azure).
  
Per altre informazioni sui dischi dati, vedere [Gestione di dischi e immagini](../../virtual-machines/windows/about-disks-and-vhds.md) e [Come collegare un disco dati da una macchina virtuale Windows ](../../virtual-machines/windows/classic/attach-disk.md).

