---
title: Supporto per l'uso di Azure Site Recovery con Backup di AzureSupport for using Azure Site Recovery with Azure Backup
description: Viene fornita una panoramica dell'utilizzo di Azure Site Recovery e Azure Backup insieme.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376224"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Supporto per l'uso di Site Recovery con Backup di AzureSupport for using Site Recovery with Azure Backup

In questo articolo viene riepilogato il supporto per l'utilizzo del [servizio Site Recovery](site-recovery-overview.md) con il servizio Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview)

**Azione** | **Supporto di Site Recovery** | **Dettagli**
--- | --- | ---
**Distribuire i servizi insiemeDeploy services together** | Supportato | I servizi sono interoperabili e possono essere configurati insieme.
**Backup/ripristino dei file** | Supportato | Quando il backup e la replica sono abilitati per una macchina virtuale e vengono eseguiti backup, non si verifica alcun problema nel ripristino dei file nelle macchine virtuali sul lato origine o nel gruppo di macchine virtuali. La replica continua come di consueto senza alcuna modifica nell'integrità della replica.
**Ripristino del disco** | Nessun supporto attuale | Se si ripristina un disco di cui è stato eseguito il backup, è necessario disabilitare e riattivare nuovamente la replica per la macchina virtuale.
**Ripristino della macchina virtuale** | Nessun supporto attuale | Se si ripristina una macchina virtuale o un gruppo di macchine virtuali, è necessario disabilitare e riabilitare la replica per la macchina virtuale.  


