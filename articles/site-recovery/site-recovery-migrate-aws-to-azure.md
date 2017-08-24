---
title: Eseguire la migrazione di macchine virtuali da AWS in Azure | Documentazione Microsoft
description: Questo articolo descrive come eseguire la migrazione di macchine virtuali in esecuzione in Amazon Web Services (AWS) ad Azure usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali in Amazon Web Services (AWS) ad Azure con Azure Site Recovery

Questo articolo descrive come eseguire la migrazione di istanze di Windows per AWS in macchine virtuali di Azure con il servizio [Azure Site Recovery](site-recovery-overview.md).

La migrazione è in realtà un failover da AWS in Azure. Non è possibile eseguire il failback di computer in AWS e non è presente alcuna replica in corso. Questo articolo descrive i passaggi per la migrazione nel portale di Azure ed è basato sulle istruzioni per la [replica di una macchina fisica di Azure](site-recovery-vmware-to-azure.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Site Recovery può essere usato per eseguire la migrazione delle istanze di EC2 in esecuzione in uno dei sistemi operativi seguenti:

- Windows (solo versione a 64 bit)
    - Windows Server 2008 R2 SP1+ (solo driver Citrix PV o driver AWS PV: **le istanze con driver RedHat PV non sono supportate**) Windows Server 2012 Windows Server 2012 R2
- Linux (solo versione a 64 bit)
    - Red Hat Enterprise Linux 6.7 (solo per istanze HVM virtualizzate)

## <a name="prerequisites"></a>Prerequisiti

Per la distribuzione è necessario quanto segue:

* **Server di configurazione**: una macchina virtuale Amazon EC2 che esegue Windows Server 2012 R2 viene distribuita come server di configurazione. Per impostazione predefinita, gli altri componenti di Azure Site Recovery (server di elaborazione e server di destinazione master) vengono installati quando si distribuisce il server di configurazione. Questo articolo descrive i passaggi per la migrazione nel portale di Azure in base alle istruzioni indicate in [Altre informazioni](site-recovery-components.md)

* **Istanze EC2**: istanze delle macchine virtuali Amazon EC2 da migrare.

## <a name="deployment-steps"></a>Passaggi di distribuzione

1. Creare un insieme di credenziali dei servizi di ripristino.
2. Il gruppo di sicurezza delle istanze di EC2 deve avere regole configurate per consentire la comunicazione tra l'istanza EC2 di cui si desidera eseguire la migrazione e l'istanza in cui si prevede di distribuire il server di configurazione.

3. Nello stesso cloud privato virtuale di Amazon delle istanze EC2 distribuire un server di configurazione di ripristino automatico di sistema. Per i requisiti di distribuzione del server di configurazione, fare riferimento ai prerequisiti per la migrazione da server VMware/fisici in Azure.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  Dopo che il server di configurazione è stato distribuito in AWS e registrato con l'insieme di credenziali di Servizi di ripristino, il server di configurazione e quello di elaborazione vengono visualizzati nell'infrastruttura di Site Recovery, come illustrato nella figura seguente:

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. Dopo avere distribuito il server di configurazione (la visualizzazione potrebbe richiedere fino a 15 minuti), verificare che possa comunicare con le macchine virtuali di cui si vuole eseguire la migrazione.

6. [Configurare le impostazioni di replica](site-recovery-setup-replication-settings-vmware.md).

7. Abilitare la replica per le macchine virtuali di cui eseguire la migrazione. È possibile individuare le istanze EC2 usando l'indirizzo IP privato che è possibile ottenere dalla console EC2.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. Dopo che le istanze EC2 sono state protette e la replica in Azure è stata completata, [eseguire un failover di test](site-recovery-test-failover-to-azure.md) per convalidare le prestazioni dell'applicazione in Azure.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Eseguire un failover da AWS in Azure per ogni macchina virtuale. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover per migrare più macchine virtuali da AWS ad Azure. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

10. Per la migrazione, non è necessario eseguire il commit di un failover. Si seleziona invece l'opzione Completa la migrazione per ogni computer di cui si vuole eseguire la migrazione. Tale opzione consente di completare il processo di migrazione e di rimuovere la replica e interrompere la fatturazione di Site Recovery per il computer.

    ![Eseguire la migrazione](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Preparare le macchine sottoposte a migrazione per abilitare la replica](site-recovery-azure-to-azure-after-migration.md) in un'altra area per il ripristino di emergenza.
- Iniziare a proteggere i carichi di lavoro [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).

