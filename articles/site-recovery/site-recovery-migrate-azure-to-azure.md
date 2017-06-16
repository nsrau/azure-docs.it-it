---
title: Eseguire la migrazione di macchine virtuali IaaS di Azure tra aree di Azure | Documentazione Microsoft
description: "Utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b0fb6e2b86aa0a47b7250face90be8ab2d06b78e
ms.contentlocale: it-it
ms.lasthandoff: 03/15/2017


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery
## <a name="overview"></a>Overview
Benvenuti in Azure Site Recovery. Consultare questo articolo se si desidera eseguire la migrazione di VM di Azure tra aree di Azure. Prima di iniziare, tenere presente quanto segue:

* Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione. I passaggi di base per la migrazione sono gli stessi indipendentemente dal fatto che si stia configurando Site Recovery in Resource Manager o nel modello classico. Tuttavia, le istruzioni e le schermate dell'interfaccia utente presenti in questo articolo sono pertinenti al portale di Azure.
* **Attualmente è possibile eseguire la migrazione solo da un'area a un'altra. Si può eseguire il failover di VM da un'area di Azure a un'altra ma non è possibile eseguire il failback.**
* Le istruzioni di migrazione in questo articolo si basano sulle istruzioni per la replica di una macchina fisica in Azure. Include i collegamenti alla procedura nell'articolo [Eseguire la replica di macchine virtuali VMware e computer fisici in Azure con Azure Site Recovery tramite il portale di Azure](site-recovery-vmware-to-azure.md), che illustra come replicare un server fisico nel portale di Azure.
* Se si sta configurando Site Recovery nel portale classico, seguire le istruzioni dettagliate in [questo articolo](site-recovery-vmware-to-azure-classic.md).

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prerequisiti
Per la distribuzione è necessario quanto segue:

* **Server di configurazione**: una VM locale che esegue Windows Server 2012 R2 e agisce da server di configurazione. Installare gli altri componenti di Site Recovery anche in questa VM, inclusi il server di elaborazione e il server di destinazione master. Per altre informazioni, leggere [Architettura dello scenario](site-recovery-components.md#vmware-to-azure) e [Prerequisiti del server di configurazione](site-recovery-vmware-to-azure.md#prerequisites).
* **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione. Nella migrazione queste VM vengono considerate macchine fisiche.

## <a name="deployment-steps"></a>Passaggi di distribuzione
Questa sezione descrive i passaggi di distribuzione nel nuovo portale di Azure.

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure.md#prepare-the-configuration-server).
3. Dopo avere distribuito il server di configurazione, verificare che possa comunicare con le VM di cui si vuole eseguire la migrazione.
4. [Configurare le impostazioni di replica](site-recovery-vmware-to-azure.md#set-up-replication-settings). Creare un criterio di replica e assegnare al server di configurazione.
5. [Installare il servizio Mobility](site-recovery-vmware-to-azure.md#prepare-vms-for-replication). In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle VM di cui si desidera eseguire la migrazione devono essere configurate per consentire l'installazione push del servizio.
6. [Abilitare la replica](site-recovery-vmware-to-azure.md#enable-replication). Abilitare la replica per le VM di cui si desidera eseguire la migrazione. È possibile individuare le macchine virtuali IaaS di cui si desidera eseguire la migrazione in Azure utilizzando il rispettivo indirizzo IP privato. Cercare questo indirizzo nel dashboard della macchina virtuale in Azure. Quando si abilita la replica, le VM vengono impostate come macchine fisiche.
7. [ Eseguire un failover non pianificato](site-recovery-failover.md). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

