---
title: Preparare le risorse di Azure per il ripristino di emergenza della soluzione Azure VMware con Azure Site Recovery
description: Informazioni su come preparare le risorse di Azure per il ripristino di emergenza delle macchine della soluzione Azure VMware con Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 83e2c46e1ce1977d0dd136e821c90843ce2de481
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814268"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-azure-vmware-solution-to-azure"></a>Preparare le risorse di Azure per il ripristino di emergenza della soluzione Azure VMware in Azure

Questo articolo descrive come preparare risorse e componenti di Azure per poter configurare il ripristino di emergenza di macchine virtuali della soluzione Azure VMware con il servizio [Azure Site Recovery](site-recovery-overview.md). La [soluzione Azure VMware](../azure-vmware/introduction.md) fornisce cloud privati in Azure. Questi cloud privati contengono cluster vSphere, creati da un'infrastruttura di Azure bare metal dedicata.

Questo articolo è la prima esercitazione di una serie che illustra come configurare il ripristino di emergenza per macchine virtuali della soluzione Azure VMware. 


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare che l'account Azure abbia le autorizzazioni di replica.
> * Creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali contiene i metadati e le informazioni di configurazione per le macchine virtuali e altri componenti di replica.
> * Configurare una rete virtuale di Azure (VNet). Quando le VM di Azure vengono create dopo il failover, vengono aggiunte a questa rete.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere l'articolo nella sezione delle procedure del sommario di Site Recovery.

> [!NOTE]
> Alcuni dei concetti relativi all'uso di Azure Site Recovery per la soluzione Azure VMware si sovrappongono con il ripristino di emergenza di macchine virtuali VMware locali e, di conseguenza, alla documentazione verrà fatto un riferimento incrociato.

## <a name="before-you-start"></a>Prima di iniziare

- [Distribuire](../azure-vmware/tutorial-create-private-cloud.md) un cloud privato della soluzione Azure VMware in Azure
- Esaminare l'architettura per il ripristino di emergenza di [VMware](vmware-azure-architecture.md)
- Vedere le domande comuni per [VMware](vmware-azure-common-questions.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione e si hanno le autorizzazioni necessarie. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere in un account di archiviazione di Azure.
- Scrivere in un disco gestito di Azure.

Per completare queste attività, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore Macchina virtuale. Per gestire le operazioni di Site Recovery in un insieme di credenziali, inoltre, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore di Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Nel menu del portale di Azure fare clic su **Crea una risorsa** e quindi cercare **Ripristino** nel Marketplace.
2. Selezionare **Backup e Site Recovery** nei risultati della ricerca e nella pagina Backup e Site Recovery fare clic su **Crea**. 
3. Nella pagina **Crea insieme di credenziali di Servizi di ripristino** selezionare **Sottoscrizione**. In questo caso verrà usata la **sottoscrizione Contoso**.
4. In **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo. Per questa esercitazione, selezionare **contosoRG**.
5. In **Nome dell'insieme di credenziali** immettere un nome descrittivo per identificare l'insieme di credenziali. Per questo set di esercitazioni viene usato **ContosoVMVault**.
6. In **Area** selezionare l'area geografica in cui dovrà essere collocato l'insieme di credenziali. Viene usato **Europa occidentale**.
7. Selezionare **Rivedi e crea**.

   ![Screenshot della pagina di creazione dell'insieme di credenziali di Servizi di ripristino.](./media/tutorial-prepare-azure/new-vault-settings.png)

   Il nuovo insieme di credenziali risulterà ora elencato in **Dashboard** > **Tutte le risorse** e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

 Le macchine virtuali della soluzione Azure VMware vengono replicate nei dischi gestiti di Azure. Quando si verifica un failover, le VM di Azure vengono create da questi dischi gestiti e aggiunti alla rete di Azure specificata in questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
2. Lasciare selezionato **Resource Manager** come modello di distribuzione.
3. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. In questa esercitazione viene usato **ContosoASRnet**.
4. In **Spazio indirizzi** immettere l'intervallo di indirizzi della rete virtuale nella notazione CIDR. In questo caso verrà usato l'indirizzo **10.1.0.0/24**.
5. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.
6. Specificare il **Gruppo di risorse** in cui verrà creata la rete. Usare il gruppo di risorse esistente **contosoRG**.
7. In **Posizione** selezionare la stessa area in cui è stato creato l'insieme di credenziali di Servizi di ripristino. In questa esercitazione si tratta di **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali.
8. In **Intervallo di indirizzi** specificare l'intervallo per la rete, in questo caso **10.1.0.0/24** senza subnet.
9. Lasciare le opzioni predefinite per la protezione DDoS di base, senza firewall o endpoint di servizio in rete.
9. Selezionare **Create** (Crea).

   ![Screenshot delle opzioni per la creazione di una rete virtuale.](media/tutorial-prepare-azure/create-network.png)

La creazione della rete virtuale richiede qualche secondo. La rete creata verrà visualizzata nel dashboard del portale di Azure.




## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Preparare l'infrastruttura](avs-tutorial-prepare-avs.md)
- [Informazioni](../virtual-network/virtual-networks-overview.md) sulle reti di Azure.
- [Informazioni](../virtual-machines/managed-disks-overview.md) sui dischi gestiti.
