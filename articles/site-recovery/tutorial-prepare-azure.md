---
title: Preparare Azure per il ripristino di emergenza di computer locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come preparare Azure per il ripristino di emergenza di computer locali con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cb2ca7229524cf8d84041140129c7b9ca6876ea3
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417801"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparare le risorse di Azure per il ripristino di emergenza di computer locali

Questo articolo descrive come preparare risorse e componenti di Azure per poter configurare il ripristino di emergenza di macchine virtuali VMware locali, macchine virtuali Hyper-V o server fisici Windows/Linux in Azure con il servizio [Azure Site Recovery](site-recovery-overview.md).

Questo articolo è la prima esercitazione di una serie che illustra come configurare il ripristino di emergenza per macchine virtuali locali. 


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'account Azure abbia le autorizzazioni di replica.
> * Creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali contiene i metadati e le informazioni di configurazione per le macchine virtuali e altri componenti di replica.
> * Configurare una rete virtuale di Azure (VNet). Quando le VM di Azure vengono create dopo il failover, vengono aggiunte a questa rete.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere l'articolo nella sezione delle procedure del sommario di Site Recovery.

## <a name="before-you-start"></a>Prima di iniziare

- Esaminare l'architettura del ripristino di emergenza per [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md) e [server fisici](physical-azure-architecture.md).
- Vedere le domande comuni per [VMware](vmware-azure-common-questions.md) e [Hyper-V](hyper-v-azure-common-questions.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione e si hanno le autorizzazioni necessarie. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere in un account di archiviazione di Azure.
- Scrivere in un disco gestito di Azure.

Per completare queste attività, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore Macchina virtuale. Per gestire le operazioni di Site Recovery in un insieme di credenziali, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore di Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Nel portale di Azure fare clic su **+Crea una risorsa** e quindi cercare **Ripristino** nel Marketplace.
2. Fare clic su **Backup e Site Recovery (OMS)** , quindi nella pagina Backup e Site Recovery fare clic su **Crea**. 
1. Nell'**insieme di credenziali di Servizi di ripristino** > **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. Per questo set di esercitazioni viene usato **ContosoVMVault**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo. Per questa esercitazione, selezionare **contosoRG**.
3. In **Località** selezionare l'area geografica in cui dovrà essere collocato l'insieme di credenziali. Viene usato **Europa occidentale**.
4. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

   ![Creare un nuovo insieme di credenziali](./media/tutorial-prepare-azure/new-vault-settings.png)

   Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

I computer locali vengono replicati in dischi gestiti di Azure. Quando si verifica un failover, le VM di Azure vengono create da questi dischi gestiti e aggiunti alla rete di Azure specificata in questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** >  **Rete** > **Rete virtuale**.
2. Lasciare selezionato **Resource Manager** come modello di distribuzione.
3. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. In questa esercitazione viene usato **ContosoASRnet**.
4. Specificare il gruppo di risorse in cui verrà creata la rete. Usare il gruppo di risorse esistente **contosoRG**.
5. In **Intervallo di indirizzi** specificare l'intervallo per la rete, in questo caso **10.1.0.0/24** senza subnet.
6. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.
7. In **Posizione** selezionare la stessa area in cui è stato creato l'insieme di credenziali di Servizi di ripristino. In questa esercitazione si tratta di **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali.
8. Lasciare le opzioni predefinite per la protezione di base DDoS, senza endpoint di servizio nella rete.
9. Fare clic su **Create**(Crea).

   ![Crea rete virtuale](media/tutorial-prepare-azure/create-network.png)

La creazione della rete virtuale richiede qualche secondo. La rete creata viene visualizzata nel dashboard del portale di Azure.




## <a name="next-steps"></a>Passaggi successivi

- [Predisporre l'infrastruttura VMware locale](tutorial-prepare-on-premises-vmware.md) per il ripristino di emergenza VMWare.
- [Predisporre i server Hyper-V locali](hyper-v-prepare-on-premises-tutorial.md) per il ripristino di emergenza Hyper-V.
- Per il ripristino di emergenza di server fisici, [configurare il server di configurazione e l'ambiente di origine](physical-azure-disaster-recovery.md)
- [Informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sulle reti di Azure.
- [Informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) sui dischi gestiti.
