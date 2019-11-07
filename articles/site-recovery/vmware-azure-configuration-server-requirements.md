---
title: 'Ripristino di emergenza di VMware: requisiti del server di configurazione in Azure Site Recovery'
description: Questo articolo descrive il supporto e i requisiti relativi alla distribuzione di un server di configurazione per il ripristino di emergenza di VMware in Azure con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663041"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisiti del server di configurazione per il ripristino di emergenza di VMware in Azure

Si distribuisce un server di configurazione locale quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure.

- Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati.
- [Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

## <a name="configuration-server-deployment"></a>Distribuzione del server di configurazione

Per il ripristino di emergenza di macchine virtuali VMware in Azure occorre distribuire il server di configurazione come macchina virtuale VMware.

- Site Recovery fornisce un modello OVA da scaricare dal portale di Azure e da importare nel server vCenter per impostare la macchina virtuale del server di configurazione.
- Quando si distribuisce il server di configurazione mediante il modello OVA, la macchina virtuale si conforma automaticamente ai requisiti elencati in questo articolo.
- È consigliabile impostare il server di configurazione con il modello OVA. Se però si sta configurando il ripristino di emergenza per le macchine virtuali VMware e non è possibile usare il modello OVA, è possibile distribuire il server di configurazione seguendo [queste istruzioni](physical-azure-set-up-source.md).
- Se si distribuisce il server di configurazione per il ripristino di emergenza di computer fisici locali in Azure, seguire le istruzioni riportate in [questo articolo](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi
Configurare il ripristino di emergenza di [macchine virtuali VMware](vmware-azure-tutorial.md) in Azure.
