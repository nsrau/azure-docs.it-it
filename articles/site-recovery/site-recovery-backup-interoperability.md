---
title: Supporto per l'uso di Azure Site Recovery con backup di Azure
description: Viene fornita una panoramica del modo in cui è possibile utilizzare insieme Azure Site Recovery e backup di Azure.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146864"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Supporto per l'uso di Site Recovery con backup di Azure

Questo articolo riepiloga il supporto per l'uso del [servizio Site Recovery](site-recovery-overview.md) insieme al [servizio backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Azione** | **Supporto Site Recovery** | **Dettagli**
--- | --- | ---
**Distribuire i servizi insieme** | Supportato | I servizi sono interoperabili e possono essere configurati insieme.
**Backup/ripristino di file** | Supportato | Quando il backup e la replica sono abilitati per una macchina virtuale e vengono eseguiti i backup, non è possibile eseguire il ripristino dei file nelle VM sul lato di origine o nel gruppo di macchine virtuali. La replica continua normalmente senza alcuna modifica all'integrità della replica.
**Backup/ripristino su disco** | Nessun supporto corrente | Se si ripristina un disco di cui è stato eseguito il backup, è necessario disabilitare e riabilitare la replica per la macchina virtuale.
**Backup/ripristino VM** | Nessun supporto corrente | Se si esegue il backup o il ripristino di una macchina virtuale o di un gruppo di macchine virtuali, è necessario disabilitare e riabilitare la replica per la macchina virtuale.  


