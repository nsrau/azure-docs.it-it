---
title: Aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Eseguire l'aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa88e45f2523dd65c4f714bfeab1c0eda401d720
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869158"
---
# <a name="operating-system-upgrade"></a>Aggiornamento del sistema operativo
Questo documento descrive i dettagli sugli aggiornamenti del sistema operativo nelle istanze HANA Large.

>[!NOTE]
>L'aggiornamento del sistema operativo è responsabilità dei clienti: il supporto di Microsoft Operations può guidare l'utente alle principali aree da osservare durante l'aggiornamento. Prima di pianificare un aggiornamento, è consigliabile consultare anche il fornitore del sistema operativo.

Al momento del provisioning unità HLI, il team operativo Microsoft installa il sistema operativo. È necessario eseguire la manutenzione del sistema operativo nel tempo (ad esempio applicando patch, eseguendo ottimizzazioni, applicando aggiornamenti e così via) nell'unità HLI.

Prima di apportare modifiche importanti al sistema operativo (ad esempio, aggiornare SP1 a SP2), è necessario contattare il team operativo Microsoft aprendo un ticket di supporto per consultarlo.

Includi nel ticket:

* L'ID di sottoscrizione HLI.
* Il nome del server.
* Il livello di patch che si prevede di applicare.
* La data in cui si prevede di effettuare la modifica. 

È consigliabile aprire il ticket almeno una settimana prima della data di aggiornamento desiderata per permettere al team di Microsoft Operations di verificare se è necessario un aggiornamento del firmware nel pannello del server.


Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemi noti

Ecco alcuni problemi comuni noti che possono presentarsi durante l'aggiornamento:
- Nello SKU classe di tipo II, il Software Foundation Server (SFS) viene rimosso dopo l'aggiornamento del sistema operativo. È necessario reinstallare il FS compatibile dopo l'aggiornamento del sistema operativo.
- I driver di scheda Ethernet (ENIC e FNIC) di cui è stato eseguito il rollback a una versione precedente. È necessario reinstallare la versione compatibile dei driver dopo l'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi
- Vedere [Backup e ripristino](hana-overview-high-availability-disaster-recovery.md) per la classe SKU di tipo I.
- Vedere [backup del sistema operativo per SKU di tipo II di revisione 3 timbri](os-backup-type-ii-skus.md) per la classe SKU di tipo II.
