---
title: Requisiti di sistema di Microsoft Azure Data Box Disk | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 20c27a3e4e9a96a19b347e5ef57ab9fb3c047140
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194436"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Requisiti di sistema di Azure Data Box Disk

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Disk e per i client connessi a Data Box Disk. Prima di distribuire Data Box Disk è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono le piattaforme supportate per i client che si connettono ai dischi, gli account di archiviazione supportati e i tipi di archiviazione.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Esaminare i prerequisiti

1. È necessario avere ordinato il Data Box Disk usando l'esercitazione: Ordinare il Azure Data Box Disk. Sono stati ricevuti i dischi e un cavo di connessione per disco.
2. Sia disponibile un computer client da cui è possibile copiare i dati. Il computer client deve:

    - Eseguire un sistema operativo supportato.
    - Installare altri software necessari.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

Di seguito è riportato l'elenco dei sistemi operativi supportati per lo sblocco del disco e l'operazione di copia dei dati eseguiti con i client connessi a Data Box Disk.

| **Sistema operativo** | **Versioni testate** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bit) |7, 8, 10 |
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

::: zone target="docs"

## <a name="supported-connection"></a>Connessione supportata

Il computer client che contiene i dati deve avere una porta USB 3.0 o versioni successive. I dischi sono connessi al cliente tramite il cavo fornito.

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per Data Box Disk.

| **Account di archiviazione** | **Note** |
| --- | --- |
| Classica | Standard |
| Utilizzo generico  |Standard; sono supportati sia V1 che V2. Sono supportati livelli ad accesso frequente e sporadico. |
| Account di archiviazione BLOB | |

>[!NOTE]
> Gli account di Azure Data Lake Storage Gen 2 non sono supportati.


## <a name="supported-storage-types-for-upload"></a>Tipi di archiviazione supportati per il caricamento

Di seguito è riportato un elenco dei tipi di archiviazione supportati per il caricamento in Azure con Data Box Disk.

| **Formato file** | **Note** |
| --- | --- |
| BLOB in blocchi di Azure | |
| BLOB di pagine di Azure  | |
| File di Azure  | |
| Managed Disks | |


## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

