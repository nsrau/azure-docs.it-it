---
title: Supporto per l'uso di Azure Site Recovery con backup di Azure
description: Viene fornita una panoramica del modo in cui è possibile utilizzare insieme Azure Site Recovery e backup di Azure.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135181"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Supporto per l'uso di Site Recovery con backup di Azure

Questo articolo riepiloga il supporto per l'uso del [servizio Site Recovery](site-recovery-overview.md) insieme al [servizio backup di Azure](../backup/backup-overview.md).

**Azione** | **Supporto di Site Recovery** | **Dettagli**
--- | --- | ---
**Distribuire i servizi insieme** | Supportato | I servizi sono interoperabili e possono essere configurati insieme.
**Backup/ripristino di file** | Supportato | Quando il backup e la replica sono abilitati per una macchina virtuale e vengono eseguiti i backup, non è possibile eseguire il ripristino dei file nelle VM sul lato di origine o nel gruppo di macchine virtuali. La replica continua normalmente senza alcuna modifica all'integrità della replica.
**Ripristino del disco** | Nessun supporto corrente | Se si ripristina un disco di cui è stato eseguito il backup, è necessario disabilitare e riabilitare nuovamente la replica per la macchina virtuale.
**Ripristino della macchina virtuale** | Nessun supporto corrente | Se si ripristina una macchina virtuale o un gruppo di macchine virtuali, è necessario disabilitare e riabilitare la replica per la macchina virtuale.  


