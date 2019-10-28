---
title: Configurare un server di elaborazione in Azure per il failback di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare un server di elaborazione in Azure per eseguire il failback di macchine virtuali di Azure in VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 080edfc86848bb6c6579c177c72d3fbd3214a06a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968845"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurare un server di elaborazione in Azure per il failback

Dopo aver eseguito il failover di macchine virtuali VMware o server fisici in Azure tramite [Site Recovery](site-recovery-overview.md), è possibile eseguirne il failback nel sito locale non appena è di nuovo disponibile. Per eseguire il failback, è necessario configurare un server di elaborazione temporaneo in Azure per gestire la replica da Azure a locale. È possibile eliminare questa macchina virtuale al termine del failback.

## <a name="before-you-start"></a>Prima di iniziare

Per altre informazioni, vedere il processo di [riprotezione](vmware-azure-reprotect.md) e [failback](vmware-azure-failback.md).

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Distribuire un server di elaborazione in Azure

1. Nell'insieme di credenziali > **Infrastruttura di Site Recovery**> **Gestisci** > **Server di configurazione** selezionare il server di configurazione.
2. Nella pagina del server fare clic su **+ Server di elaborazione**
3. Nella pagina **Aggiungere il server di elaborazione** selezionare di distribuire il server di elaborazione in Azure.
4. Specificare le impostazioni di Azure, tra cui la sottoscrizione usata per il failover, un gruppo di risorse, l'area di Azure usata per il failover e la rete virtuale in cui si trovano le macchine virtuali di Azure. Se si usano più reti di Azure, è necessario un server di elaborazione in ciascuna di esse.

   ![Elemento della raccolta per l'aggiunta del server di elaborazione](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. In **Nome server**, **Nome utente**e **Password** specificare un nome per il server di elaborazione e le credenziali a cui verranno assegnate le autorizzazioni di amministrazione sul server.
5. Specificare un account di archiviazione da usare per i dischi della macchina virtuale del server, la subnet in cui la macchina virtuale del server di elaborazione sarà disponibile e l'indirizzo IP del server che verrà assegnato all'avvio della macchina virtuale.
6. Fare clic sul pulsante **OK** per avviare la distribuzione della macchina virtuale del server di elaborazione. Il server di elaborazione verrà distribuito nello SKU Standard_A8_v2. Assicurarsi che lo SKU della macchina virtuale sia disponibile per la sottoscrizione.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrazione del server di elaborazione in esecuzione in Azure con un server di configurazione locale

Quando la macchina virtuale del server di elaborazione è disponibile, è necessario registrarla con il server di configurazione locale come descritto di seguito:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


