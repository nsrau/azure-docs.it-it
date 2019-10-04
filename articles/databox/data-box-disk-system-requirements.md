---
title: Requisiti di sistema di Microsoft Azure Data Box Disk | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fb2fd89664517e44cf5128a5c82e583f03087061
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307689"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Requisiti di sistema di Azure Data Box Disk

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Disk e per i client connessi a Data Box Disk. Prima di distribuire Data Box Disk è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono le piattaforme supportate per i client che si connettono ai dischi, gli account di archiviazione supportati e i tipi di archiviazione.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Esaminare i prerequisiti

1. È necessario aver ordinato Data Box Disk seguendo le indicazioni dell'[Esercitazione: ](data-box-disk-deploy-ordered.md)Ordinare Azure Data Box Disk. Oltre ai dischi è stato ricevuto un cavo di collegamento per ogni disco.
2. Sia disponibile un computer client da cui è possibile copiare i dati. Il computer client deve:

    - Eseguire un sistema operativo supportato.
    - Includere eventuale altro software richiesto.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

Di seguito è riportato l'elenco dei sistemi operativi supportati per lo sblocco del disco e l'operazione di copia dei dati eseguiti con i client connessi a Data Box Disk.

| **Sistema operativo** | **Versioni testate** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (a 64 bit) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Altri software obbligatori per i client Windows

Per i client Windows, è consigliabile installare anche i seguenti.

| **Software**| **Versione** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Altri software obbligatori per i client Linux

Per client Linux, il set di strumenti di Data Box Disk installa il seguente software obbligatorio:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Connessione supportata

Il computer client che contiene i dati deve avere una porta USB 3.0 o versioni successive. I dischi sono connessi al cliente tramite il cavo fornito.

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per Data Box Disk.

| **Account di archiviazione** | **Note** |
| --- | --- |
| Classico | Standard |
| Utilizzo generico  |Standard; sono supportati sia V1 che V2. Sono supportati livelli ad accesso frequente e sporadico. |
| Account di archiviazione BLOB | |

>[!NOTE]
> Gli account di Azure Data Lake Storage Gen 2 non sono supportati.


## <a name="supported-storage-types-for-upload"></a>Tipi di archiviazione supportati per il caricamento

Ecco un elenco dei tipi di archiviazione supportati per il caricamento in Azure con Data Box Disk.

| **Formato file** | **Note** |
| --- | --- |
| BLOB in blocchi di Azure | |
| BLOB di pagine di Azure  | |
| File di Azure  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

