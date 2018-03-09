---
title: Configurare l'ambiente di origine (VMware in Azure) | Documentazione Microsoft
description: Questo articolo descrive come configurare l'ambiente locale per avviare la replica di macchine virtuali VMware in Azure.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurare l'ambiente di origine (VMware in Azure)
> [!div class="op_single_selector"]
> * [Da VMware ad Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Da fisico ad Azure](./site-recovery-set-up-physical-to-azure.md)

Questo articolo descrive come configurare l'ambiente locale di origine per la replica di macchine virtuali in esecuzione su VMware in Azure. Include i passaggi per la selezione dello scenario di replica, l'impostazione di un computer locale come server di configurazione di Site Recovery e l'individuazione automatica delle macchine virtuali locali. 

## <a name="prerequisites"></a>prerequisiti

Nell'articolo si presuppone che le seguenti procedure siano già state eseguite:
- [Configurare le risorse ](tutorial-prepare-azure.md) nel [portale di Azure](http://portal.azure.com).
- [Configurare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md), tra cui un account dedicato per l'individuazione automatica.



## <a name="choose-your-protection-goals"></a>Scegliere gli obiettivi della protezione

1. Nel portale di Azure accedere al pannello degli insiemi di credenziali di **Servizi di ripristino** e selezionare l'insieme di credenziali.
2. Nel menu delle risorse dell'insieme di credenziali passare ad **Attività iniziali** > **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

Il server di configurazione è impostato come una macchina virtuale VMware locale, usando un modello OVF (Open Virtualization Format). [Altre informazioni](concepts-vmware-to-azure-architecture.md) sui componenti che verranno installati nella macchina virtuale VMware. 

1. Informazioni sui [prerequisiti](how-to-deploy-configuration-server.md#prerequisites) per la distribuzione del server di configurazione. [Controllare i valori di capacità](how-to-deploy-configuration-server.md#capacity-planning) per la distribuzione.
2. [Scaricare](how-to-deploy-configuration-server.md#download-the-template) e [importare](how-to-deploy-configuration-server.md#import-the-template-in-vmware) il modello OVF (how-to-deploy-configuration-server.md) per configurare una macchina virtuale VMware locale che esegue il server di configurazione.
3. Attivare la macchina virtuale VMware e [registrarla](how-to-deploy-configuration-server.md#register-the-configuration-server) nell'insieme di credenziali di Servizi di ripristino.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Aggiungere l'account VMware per l'individuazione automatica

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Connettersi al server VMware

Per consentire ad Azure Site Recovery di individuare macchine virtuali in esecuzione nell'ambiente locale, è necessario connettere il server VMware vCenter o gli host vSphere ESXi a Site Recovery.

Selezionare **+vCenter** per avviare la connessione di un server VMware vCenter o di un host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemi comuni
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passaggi successivi
[Configurare l'ambiente di destinazione](./site-recovery-prepare-target-vmware-to-azure.md) in Azure.
