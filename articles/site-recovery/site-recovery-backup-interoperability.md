---
title: Supporto per l'uso di Azure Site Recovery con Backup di Azure | Microsoft Docs
description: Viene fornita una panoramica del modo in cui Azure Site Recovery e Backup di Azure possono essere usati insieme.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312887"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Supporto per l'uso di Site Recovery con Backup di Azure

Questo articolo viene riepilogato il supporto per l'uso di [servizio Site Recovery](site-recovery-overview.md) insieme al [servizio Backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Azione** | **Supporto di Site Recovery** | **Dettagli**
--- | --- | ---
**Distribuire i servizi insieme** | Supportato | Servizi di interoperabilità e possono essere configurati tra loro.
**File di backup/ripristino** | Supportato | Quando backup e la replica sono abilitati per una macchina virtuale e i backup vengono eseguiti, in viene visualizzato alcun problema di ripristino di file sul lato di origine le macchine virtuali, o gruppo di macchine virtuali. La replica continua come di consueto senza alcuna modifica nell'integrità della replica.
**Backup/ripristino del disco** | Nessun supporto corrente | Se si ripristina un backup disco, è necessario disabilitare e riabilitare la replica per la macchina virtuale.
**Backup/ripristino di macchine Virtuali** | Nessun supporto corrente | Se per il backup o ripristinare una macchina virtuale o un gruppo di macchine virtuali, è necessario disabilitare e riabilitare la replica per la macchina virtuale.  


