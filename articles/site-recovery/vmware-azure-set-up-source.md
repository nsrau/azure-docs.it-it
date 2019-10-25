---
title: Configurare l'ambiente di origine per la replica da VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare l'ambiente locale per la replica di macchine virtuali VMware in Azure con Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: bf1ff4dfba105b6c90ab949217453e1db82d109d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791765"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurare l'ambiente di origine per la replica da VMware in Azure

Questo articolo descrive come configurare l'ambiente locale di origine per la replica di macchine virtuali VMware in Azure. L'articolo include i passaggi per selezionare lo scenario di replica, configurare un computer locale come server di configurazione Site Recovery e individuare automaticamente le macchine virtuali locali.

## <a name="prerequisites"></a>Prerequisiti

Nell'articolo si presuppone che le seguenti procedure siano già state eseguite:

- Pianificazione della distribuzione con l'ausilio di [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). In questo modo è possibile allocare larghezza di banda sufficiente, in base alla frequenza di modifica dei dati giornaliera, per soddisfare l'obiettivo del punto di ripristino (RPO) desiderato.
- [Configurare le risorse ](tutorial-prepare-azure.md) nel [portale di Azure](https://portal.azure.com).
- [Configurare istanze di VMware locali](vmware-azure-tutorial-prepare-on-premises.md), tra cui un account dedicato per l'individuazione automatica.

## <a name="choose-your-protection-goals"></a>Scegliere gli obiettivi della protezione

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare il nome dell'insieme di credenziali. In questo scenario viene usato **ContosoVMVault**.
2. In **Attività iniziali** selezionare su Site Recovery. Selezionare quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **I computer sono virtuali** selezionare **Sì con VMware vSphere Hypervisor**. Selezionare **OK**.

## <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

È possibile configurare il server di configurazione come una macchina virtuale VMware locale usando un modello OVA (Open Virtualization Application). [Altre informazioni](concepts-vmware-to-azure-architecture.md) sui componenti che verranno installati nella macchina virtuale VMware.

1. Informazioni sui [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites) per la distribuzione del server di configurazione.
2. [Controllare i valori di capacità](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) per la distribuzione.
3. [Scaricare](vmware-azure-deploy-configuration-server.md#download-the-template) e [importare](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) il modello OVA per configurare una macchina virtuale VMware locale che esegue il server di configurazione. La licenza fornita con il modello è una licenza di valutazione ed è valida per 180 giorni. Dopo questo periodo, il cliente deve attivare le finestre con una licenza acquistata.
4. Attivare la macchina virtuale VMware e [registrarla](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) nell'insieme di credenziali di Servizi di ripristino.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Esclusioni della cartella Azure Site Recovery dal programma antivirus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Se il software antivirus è attivo nel computer di origine

Se sul computer di origine è presente un software antivirus attivo, è necessario escludere la cartella di installazione. Pertanto, escludere la cartella *C:\ProgramData\ASR\agent* per la replica senza problemi.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Se il software antivirus è attivo nel server di configurazione

Escludere le cartelle seguenti dal software antivirus per la replica senza problemi e per evitare problemi di connettività

- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure.
- C:\Programmi\Provider di Servizi di ripristino di Microsoft Azure
- C:\Programmi\Gestione configurazione di Servizi di ripristino di Microsoft Azure 
- C:\Programmi\Error Collection Tool di Servizi di ripristino di Microsoft Azure 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Programmi (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Directory di installazione di Site Recovery Server. Ad esempio: E:\Programmi (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Se il software antivirus è attivo sul server di elaborazione scale-out/database di destinazione master

Escludere le cartelle seguenti dal software antivirus

1. C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery directory di installazione del server di elaborazione con bilanciamento del carico, ad esempio: C:\Programmi (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Passaggi successivi
[Configurare l'ambiente di destinazione](./vmware-azure-set-up-target.md) 
