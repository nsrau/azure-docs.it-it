---
title: Eseguire la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra con Site Recovery | Microsoft Docs
description: Utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: tysonn

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2016
ms.author: raynew

---
# Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery
## Overview
Benvenuti in Azure Site Recovery. Consultare questo articolo se si desidera eseguire la migrazione di VM di Azure tra aree di Azure. Prima di iniziare, tenere presente quanto segue:

* Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione. I passaggi di base per la migrazione sono gli stessi indipendentemente dal fatto che si stia configurando Site Recovery in Resource Manager o nel modello classico. Tuttavia, le istruzioni e le schermate dell'interfaccia utente presenti in questo articolo sono pertinenti al portale di Azure.
* **Attualmente è possibile eseguire la migrazione solo da un'area a un'altra. Si può eseguire il failover di VM da un'area di Azure a un'altra ma non è possibile eseguire il failback.**
* Le istruzioni di migrazione in questo articolo si basano sulle istruzioni per la replica di una macchina fisica in Azure. Include i collegamenti alla procedura nell'articolo [Eseguire la replica di macchine virtuali VMware e computer fisici in Azure con Azure Site Recovery tramite il portale di Azure](site-recovery-vmware-to-azure.md), che illustra come replicare un server fisico nel portale di Azure.
* Se si sta configurando Site Recovery nel portale classico, seguire le istruzioni dettagliate in [questo articolo](site-recovery-vmware-to-azure-classic.md). **Non usare più** le istruzioni contenute in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Prerequisiti
Per la distribuzione è necessario quanto segue:

* **Server di configurazione**: una VM locale che esegue Windows Server 2012 R2 e agisce da server di configurazione. Installare gli altri componenti di Site Recovery anche in questa VM, inclusi il server di elaborazione e il server di destinazione master. Per altre informazioni, leggere [Architettura dello scenario](site-recovery-vmware-to-azure.md#scenario-architecture) e [Prerequisiti del server di configurazione](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
* **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione. Nella migrazione queste VM vengono considerate macchine fisiche.

## Passaggi di distribuzione
Questa sezione descrive i passaggi di distribuzione nel nuovo portale di Azure. Se questi passaggi di distribuzione sono richiesti per Site Recovery nel portale classico, fare riferimento a [questo articolo](site-recovery-vmware-to-azure-classic.md).

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Dopo avere distribuito il server di configurazione, verificare che possa comunicare con le VM di cui si vuole eseguire la migrazione.
4. [Configurare le impostazioni di replica](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Creare un criterio di replica e assegnare al server di configurazione.
5. [Installare il servizio Mobility](site-recovery-vmware-to-azure.md#step-6-replication-application). In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle VM di cui si desidera eseguire la migrazione devono essere configurate per consentire l'installazione push del servizio.
6. [Abilitare la replica](site-recovery-vmware-to-azure.md#enable-replication). Abilitare la replica per le VM di cui si desidera eseguire la migrazione. È possibile individuare le macchine virtuali IaaS di cui si desidera eseguire la migrazione in Azure utilizzando il rispettivo indirizzo IP privato. Cercare questo indirizzo nel dashboard della macchina virtuale in Azure. Quando si abilita la replica, le VM vengono impostate come macchine fisiche.
7. [ Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0824_2016-->