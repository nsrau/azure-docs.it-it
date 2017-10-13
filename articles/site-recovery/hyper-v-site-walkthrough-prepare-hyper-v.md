---
title: Preparare gli host Hyper-V (senza System Center VMM) per la replica in Azure | Microsoft Docs
description: Viene descritto come preparare gli host Hyper-V per la replica in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Passaggio 6: Preparare gli host Hyper-V per la replica in Azure

Usare le istruzioni riportate in questo articolo per preparare gli host Hyper-V locali per l'interazione con Azure Site Recovery.

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Preparare gli host

- Assicurarsi che gli host Hyper-V soddisfino i [prerequisiti](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Assicurarsi che gli host possano accedere agli URL necessari:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
- Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
- Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

Durante la distribuzione di Site Recovery, aggiungere gli host Hyper-V che contengono le VM da replicare in un sito Hyper-V. Il provider di Site Recovery e l'agente di Servizi di ripristino sono installati in ogni host. Il sito Hyper-V è registrato nell'insieme di credenziali di Servizi di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 7: Creare un insieme di credenziali](hyper-v-site-walkthrough-create-vault.md)

