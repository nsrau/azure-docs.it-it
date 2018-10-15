---
title: Requisiti di sistema di Microsoft Azure Data Box Disk | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box Disk
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
ms.workload: NA
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: aaa4e4bb24ca42adb9d283e6286dbef879bcb1ea
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299849"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Requisiti di sistema di Azure Data Box Disk (anteprima)

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Disk e per i client connessi a Data Box Disk. Prima di distribuire Data Box Disk è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

> [!IMPORTANT]
> Data Box Disk è disponibile in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

I requisiti di sistema includono le piattaforme supportate per i client che si connettono ai dischi, gli account di archiviazione supportati e i tipi di archiviazione.


## <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

Di seguito è riportato l'elenco dei sistemi operativi supportati per lo sblocco del disco e l'operazione di copia dei dati eseguiti con i client connessi a Data Box Disk.

| **Sistema operativo** | **Versioni testate** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
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

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per Data Box Disk.

| **Account di archiviazione** | **Note** |
| --- | --- |
| Classico | Standard |
| Utilizzo generico  |Standard; sono supportati sia V1 che V2. Sono supportati livelli ad accesso frequente e sporadico. |


## <a name="supported-storage-types"></a>Tipi di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per Data Box Disk.

| **Formato file** | **Note** |
| --- | --- |
| BLOB in blocchi di Azure | |
| BLOB di pagine di Azure  | |


## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Disk](data-box-disk-deploy-ordered.md)

