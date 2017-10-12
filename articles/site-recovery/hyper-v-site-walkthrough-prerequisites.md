---
title: Esaminare i prerequisiti per la replica Hyper-V in Azure (senza System Center VMM) usando Azure Site Recovery | Microsoft Docs
description: Vengono descritti i prerequisiti per la configurazione di replica, failover e ripristino di VM Hyper-V locali in Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>Passaggio 2: Esaminare i prerequisiti per la replica Hyper-V (senza VMM) in Azure

I prerequisiti sono riepilogati nella tabella.


**Prerequisito** | **Dettagli** 
--- | --- 
**Azure** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server locali** | Leggere altre informazioni sui requisiti per gli host Hyper-V locali [qui](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
**VM Hyper-V locali** | Le VM da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL di Azure** | Gli host Hyper-V devono accedere agli URL seguenti:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).



## <a name="next-steps"></a>Passaggi successivi

- Se si sta eseguendo una distribuzione completa, andare a [Passaggio 3: Pianificare la capacità](hyper-v-site-walkthrough-capacity.md)
- Se si sta eseguendo una distribuzione di test semplice, andare a [Passaggio 4: Pianificare la rete](hyper-v-site-walkthrough-network.md).
