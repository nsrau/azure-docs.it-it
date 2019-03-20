---
title: Azure Site Recovery - interoperabilità Backup | Microsoft Docs
description: Viene fornita una panoramica del modo in cui Azure Site Recovery e Backup di Azure possono essere usati insieme.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731873"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Su Site Recovery e l'interoperabilità di Backup

Questo articolo fornisce indicazioni per usando correttamente il Backup di macchine Virtuali IaaS di Azure e macchine Virtuali di Azure di ripristino di emergenza.

## <a name="azure-backup"></a>Backup di Azure

Backup di Azure consente di proteggere i dati per server locali, macchine virtuali, carichi di lavoro virtualizzati, istanze di SQL Server, SharePoint Server e altro ancora. Azure Site Recovery Orchestra e gestisce il ripristino di emergenza per macchine virtuali di Azure, macchine virtuali locali e server fisici.

## <a name="azure-site-recovery"></a>Azure Site Recovery

È possibile configurare il Backup di Azure e Azure Site Recovery su una macchina virtuale o un gruppo di macchine virtuali. Entrambi i prodotti sono interoperativi. Alcuni scenari in cui l'interoperabilità tra Backup e Site Recovery di Azure diventa importante sono i seguenti:

### <a name="file-backuprestore"></a>File di Backup/ripristino

Se il Backup e la replica sono entrambi abilitati e viene eseguito un backup, non c'è Nessun problema con il ripristino di qualsiasi file nella macchina virtuale sul lato di origine o il gruppo di macchine virtuali. Replica continuerà normalmente senza alcuna modifica nell'integrità della replica.

### <a name="disk-backuprestore"></a>Backup/ripristino del disco

Se si ripristinano dischi dal backup protezione della macchina virtuale deve essere abilitato nuovamente.

### <a name="vm-backuprestore"></a>Backup/ripristino di macchine Virtuali

Backup e ripristino di una macchina virtuale o un gruppo di macchine virtuali non è supportato. Per il funzionamento, la protezione deve essere abilitato nuovamente.

**Scenario** | **Supportato da Azure Site Recovery?** | **Soluzione alternativa, se presente**  
--- | --- | ---
Backup di file/cartelle | Sì | Non applicabile
Backup su disco | Non è attualmente | Disabilitare e riabilitare la protezione
Backup di macchine Virtuali | No  | Disabilitare e riabilitare la protezione
