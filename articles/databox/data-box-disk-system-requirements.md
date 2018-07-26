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
ms.date: 07/10/2018
ms.author: alkohli
ms.openlocfilehash: 7138fa70c8b5615ad84196703f3bd76009ba5811
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011489"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Requisiti di sistema di Azure Data Box Disk (anteprima)

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Disk e per i client connessi a Data Box Disk. Prima di distribuire Data Box Disk è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

> [!IMPORTANT]
> Data Box Disk è disponibile in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

I requisiti di sistema includono le piattaforme supportate per i client che si connettono ai dischi, gli account di archiviazione supportati e i tipi di archiviazione.


## <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

Di seguito è riportato l'elenco dei sistemi operativi supportati per lo sblocco del disco e l'operazione di copia dei dati eseguiti con i client connessi a Data Box Disk.

| **Sistema operativo/piattaforma** | **Versioni** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4.0 |
| .NET Framework |4.5 |

> [!NOTE] 
> BitLocker deve essere abilitata nei client che eseguono lo strumento di sblocco del disco e vengono usati per copiare i dati.


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

