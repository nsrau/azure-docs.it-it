---
title: Installare il servizio Mobility per la replica VMware in Azure | Microsoft Docs
description: Questo articolo illustra come installare l'agente del servizio Mobility per la replica VMware in Azure con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---

# <a name="step-10-install-the-mobility-service"></a>Passaggio 10: Installare il servizio Mobility


Questo articolo illustra come configurare le impostazioni di origine e di destinazione per la replica di macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Il servizio Mobility acquisisce le scritture di dati in un computer e le inoltra al server di elaborazione. Questo servizio deve essere installato in ogni computer di cui si vuole eseguire la replica in Azure.

È possibile installare il servizio Mobility manualmente, usando un'installazione push dal server di elaborazione di Site Recovery o usando lo strumento System Center Configuration Manager. Se si usa l'installazione push, il servizio viene installato nella VM quando viene abilitata la replica.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Eseguire l'installazione manuale

1. Controllare i [prerequisiti](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) per l'installazione manuale.
2. Seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) per l'installazione manuale tramite il portale.
3. Se si preferisce eseguire l'installazione dalla riga di comando, seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Eseguire l'installazione dal server di elaborazione

Se si vuole effettuare il push dell'installazione del servizio Mobility dal server di elaborazione quando si abilita la replica per una VM, è necessario un account che possa essere usato dal server di elaborazione per accedere alla VM. L'account viene usato solo per l'installazione push.

1. È necessario aver [creato un account](vmware-walkthrough-prepare-vmware.md) che possa essere usato per l'installazione push. È quindi possibile specificare l'account da usare quando si configurano le impostazioni di origine durante la distribuzione di Site Recovery.
2. Seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) se si vuole effettuare il push del servizio Mobility nelle VM che eseguono Windows o Linux.

## <a name="other-methods"></a>Altri metodi

Leggere le informazioni sull'[installazione del servizio Mobility usando Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) o [Automation DSC per Azure](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 11: Abilitare la replica](vmware-walkthrough-enable-replication.md)

