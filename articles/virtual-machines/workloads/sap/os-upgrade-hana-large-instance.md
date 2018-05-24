---
title: Aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Eseguire l'aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077746"
---
# <a name="operating-system-upgrade"></a>Aggiornamento del sistema operativo
Questo documento descrive i dettagli sugli aggiornamenti del sistema operativo nelle istanze HANA Large.

>[!NOTE]
>L'aggiornamento del sistema operativo è responsabilità dei clienti: il supporto di Microsoft Operations può guidare l'utente alle principali aree da osservare durante l'aggiornamento. Prima di pianificare un aggiornamento, è consigliabile consultare anche il fornitore del sistema operativo.

Al momento del provisioning dell'unità HLI, il team di Microsoft Operations installa il sistema operativo. È necessario mantenere il sistema operativo nel tempo (ad esempio applicando patch, eseguendo ottimizzazioni, applicando aggiornamenti e così via) nell'unità HLI.

Prima di apportare modifiche importanti al sistema operativo (ad esempio prima di aggiornarlo), **è necessario** controllare la seguente matrice di compatibilità. Prima di avviare attività importanti del sistema operativo come l'aggiornamento, **è necessario** anche consultare il team di Microsoft Operations aprendo un ticket di supporto.

Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).

La compatibilità seguente è stata testata per le unità HLI. Se il server HLI non rientra nella matrice di compatibilità, contattare il supporto di Microsoft Operations.

## <a name="for-type-i-class-sku-category"></a>Per la categoria SKU classe di tipo I

| Configurazione | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Firmware del server | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| Versione ENIC | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| Versione FNIC | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Disabled | Disabled | Disabled | Disabled |
| Versione del kernel | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Per la categoria SKU classe di tipo II

| Configurazione | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Versione firmware RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Versione firmware BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Versione Software Foundation Server (SFS) | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| Versione i40e    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Versione del kernel    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Problemi noti

Ecco alcuni problemi comuni noti che possono presentarsi durante l'aggiornamento:
- Nello SKU classe di tipo II, il Software Foundation Server (SFS) viene rimosso dopo l'aggiornamento del sistema operativo. È necessario reinstallare il SFS compatibile dopo l'aggiornamento del sistema operativo.
- I driver di scheda Ethernet (ENIC e FNIC) di cui è stato eseguito il rollback a una versione precedente. Dopo l'aggiornamento è necessario reinstallare la versione compatibile dei driver.

## <a name="next-steps"></a>Passaggi successivi
- Vedere [Backup e ripristino](hana-overview-high-availability-disaster-recovery.md) per la classe SKU di tipo I.
- Vedere [Backup del sistema operativo per SKU di tipo II](os-backup-type-ii-skus.md) per la classe SKU di tipo II.