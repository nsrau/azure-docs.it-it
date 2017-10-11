---
title: Preparare System Center VMM per la replica Hyper-V in Azure | Microsoft Docs
description: Illustra come preparare un server System Center VMM per la replica Hyper-V in Azure tramite Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Passaggio 6: Preparare i server VMM e gli host Hyper-V per la replica Hyper-V in Azure

Al termine della configurazione dei [componenti di Azure](vmm-to-azure-walkthrough-prepare-azure.md) per la distribuzione, usare le istruzioni disponibili in questo articolo per preparare i server VMM locali e gli host Hyper-V per l'interazione con Azure Site Recovery.

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Preparare i server VMM

- È necessario almeno un server VMM che soddisfi i requisiti di supporto per la replica tramite Site Recovery (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Assicurarsi di avere preparato il server VMM per il [mapping di rete](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Assicurarsi che il server VMM possa accedere a questi URL

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
- Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
- Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

Durante la distribuzione di Site Recovery, il provider di Site Recovery viene scaricato e installato in ogni server VMM. Il server VMM è registrato nell'insieme di credenziali di Servizi di ripristino.




## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 7: Creare un insieme di credenziali](vmm-to-azure-walkthrough-create-vault.md)

