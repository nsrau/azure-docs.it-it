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
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali in Amazon Web Services (AWS) ad Azure con Azure Site Recovery

Questo articolo descrive come eseguire la migrazione di istanze di Windows per AWS in macchine virtuali di Azure con il servizio [Azure Site Recovery](site-recovery-overview.md).

La migrazione è in realtà un failover da AWS in Azure. Non è possibile eseguire il failback delle macchine e non vi è alcuna replica continuativa. Questo articolo descrive i passaggi per la migrazione nel portale di Azure ed è basato sulle istruzioni per la [replica di una macchina fisica di Azure](site-recovery-vmware-to-azure.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Site Recovery può essere usato per eseguire la migrazione delle istanze di EC2 in esecuzione in uno dei sistemi operativi seguenti:

- Windows (solo versione a&64; bit)
    - Windows Server 2008 R2 SP1+ (solo driver Citrix PV o driver AWS PV: **le istanze con driver RedHat PV non sono supportate**) Windows Server 2012 Windows Server 2012 R2
- Linux (solo versione a&64; bit)
    - Red Hat Enterprise Linux 6.7 (solo per istanze HVM virtualizzate)

## <a name="prerequisites"></a>Prerequisiti

Per la distribuzione è necessario quanto segue

* **Server di configurazione**: una VM locale che esegue Windows Server 2012 R2 viene distribuita come server di configurazione. Per impostazione predefinita, gli altri componenti di Site Recovery (server di elaborazione e server di destinazione master) vengono installati quando si distribuisce il server di configurazione. [Altre informazioni](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **Istanze di EC2**: le istanze delle macchine virtuali EC2 da migrare.

## <a name="deployment-steps"></a>Passaggi di distribuzione

1. Creare un insieme di credenziali
2. Distribuire il server di configurazione
3. Dopo avere distribuito il server di configurazione, verificare che possa comunicare con le VM di cui si vuole eseguire la migrazione.
4. Configurare le impostazioni di replica.
5. Installare il servizio Mobility. In ogni VM che si desidera proteggere deve essere installato il servizio di mobilità. Il servizio invia i dati al server di elaborazione. Il servizio di mobilità può essere installato manualmente o inserito e installato automaticamente dal server di elaborazione quando la protezione per la VM è abilitata. Le regole del firewall nelle istanze EC2 di cui si desidera eseguire la migrazione devono essere configurate per consentire l’installazione push del servizio. Il gruppo di sicurezza delle istanze EC2 deve avere le seguenti regole:

    ![Regole del firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. Abilitare la replica. Abilitare la replica per le VM di cui si desidera eseguire la migrazione. È possibile individuare le istanze EC2 usando l'indirizzo IP privato che è possibile ottenere dalla console EC2.
7. Eseguire un failover non pianificato. Al termine della replica iniziale, è possibile eseguire un failover non pianificato da AWS ad Azure per ogni VM. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali da AWS ad Azure. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

Ottenere istruzioni dettagliate per i [passaggi di distribuzione](site-recovery-vmware-to-azure.md) e per l'esecuzione di un [failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover).



<!--HONumber=Feb17_HO2-->


