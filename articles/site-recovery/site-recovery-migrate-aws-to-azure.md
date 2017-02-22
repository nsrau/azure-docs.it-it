---
title: Eseguire la migrazione di macchine virtuali da AWS in Azure | Documentazione Microsoft
description: Questo articolo descrive come eseguire la migrazione di macchine virtuali in esecuzione in Amazon Web Services (AWS) ad Azure usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: 5cf068dd64084d39e353dab6629e832112e15843


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali in Amazon Web Services (AWS) ad Azure con Azure Site Recovery
## <a name="overview"></a>Overview
Benvenuti in Azure Site Recovery. Consultare questo articolo per eseguire la migrazione di istanze di EC2 in esecuzione in AWS ad Azure con Site Recovery. Prima di iniziare, tenere presente quanto segue:

* Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione. I passaggi di base per la migrazione sono gli stessi indipendentemente dal fatto che si stia configurando Site Recovery in Resource Manager o nel modello classico. Tuttavia, le istruzioni e le schermate dell'interfaccia utente presenti in questo articolo sono pertinenti al portale di Azure.
* **Attualmente è possibile eseguire la migrazione solo da AWS in Azure. Si può eseguire il failover di VM da AWS ad Azure ma non è possibile eseguire il failback. Non esiste alcuna replica in corso.**
* Le istruzioni di migrazione in questo articolo si basano sulle istruzioni per la replica di una macchina fisica in Azure. Include i collegamenti alla procedura nell'articolo [Eseguire la replica di macchine virtuali VMware e computer fisici in Azure con Azure Site Recovery tramite il portale di Azure](site-recovery-vmware-to-azure.md), che illustra come replicare un server fisico nel portale di Azure.
* Se si sta configurando Site Recovery nel portale classico, seguire le istruzioni dettagliate in [questo articolo](site-recovery-vmware-to-azure-classic.md). **Non usare più** le istruzioni contenute in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Site Recovery può essere usato per eseguire la migrazione delle istanze di EC2 in esecuzione in uno dei sistemi operativi seguenti

### <a name="windows64-bit-only"></a>Windows (solo versione a&64; bit)
* Windows Server 2008 R2 SP1 + (solo driver PV Citrix o PV AWS; **non sono supportate istanze che eseguono driver PV RedHat**)
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (solo versione a&64; bit)
* Red Hat Enterprise Linux 6.7 (solo per istanze HVM virtualizzate)

## <a name="prerequisites"></a>Prerequisiti
Per la distribuzione è necessario quanto segue

* **Server di configurazione**: una VM locale che esegue Windows Server 2012 R2 e agisce da server di configurazione. Installare gli altri componenti di Site Recovery anche in questa VM, inclusi il server di elaborazione e il server di destinazione master. Per altre informazioni, leggere [Architettura dello scenario](site-recovery-vmware-to-azure.md#scenario-architecture) e [Prerequisiti del server di configurazione](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).
* **Istanze di macchine virtuali EC2**: le istanze EC2 di cui si desidera eseguire la migrazione.

## <a name="deployment-steps"></a>Passaggi di distribuzione
Questa sezione descrive i passaggi di distribuzione nel nuovo portale di Azure. Se questi passaggi di distribuzione sono richiesti per Site Recovery nel portale classico, fare riferimento a [questo articolo](site-recovery-vmware-to-azure-classic.md).

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Dopo avere distribuito il server di configurazione, verificare che possa comunicare con le VM di cui si vuole eseguire la migrazione.
4. [Configurare le impostazioni di replica](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Creare un criterio di replica e assegnare al server di configurazione.
5. [Installare il servizio Mobility](site-recovery-vmware-to-azure.md#step-6-replicate-applications). In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle istanze EC2 di cui si desidera eseguire la migrazione devono essere configurate per consentire l’installazione push del servizio. Il gruppo di sicurezza delle istanze EC2 deve avere le seguenti regole:

    ![Regole del firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. [Abilitare la replica](site-recovery-vmware-to-azure.md#enable-replication). Abilitare la replica per le VM di cui si desidera eseguire la migrazione. È possibile individuare le istanze EC2 usando l'indirizzo IP privato che è possibile ottenere dalla console EC2.
7. [ Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Al termine della replica iniziale, è possibile eseguire un failover non pianificato da AWS ad Azure per ogni VM. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali da AWS ad Azure. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)



<!--HONumber=Jan17_HO5-->


