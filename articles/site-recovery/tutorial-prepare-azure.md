---
title: Preparare Azure per il ripristino di emergenza di computer locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come preparare Azure per il ripristino di emergenza di computer locali con Azure Site Recovery.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/03/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5168fc28952631f00c2415d6bc171a130dc85dfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838562"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparare le risorse di Azure per il ripristino di emergenza di computer locali

 [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questo articolo è la prima esercitazione di una serie che illustra come configurare il ripristino di emergenza per macchine virtuali locali. È utile se si vogliono proteggere VM VMware locali, VM Hyper-V o server fisici.

> [!NOTE]
> Le esercitazioni sono progettate per illustrare il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, consultare la sezione **Procedure** per lo scenario corrispondente.

Questo articolo spiega come preparare i componenti di Azure quando si vuole eseguire la replica di macchine virtuali locali (Hyper-V o VMware) o server fisici Windows/Linux in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'account Azure abbia le autorizzazioni di replica.
> * Creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali contiene i metadati e le informazioni di configurazione per le macchine virtuali e altri componenti di replica.
> * Configurare una rete di Azure. Le macchine virtuali di Azure create dopo il failover vengono aggiunte a questa rete di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere nell'account di archiviazione.
- Scrivere nel disco gestito.

Per completare queste attività, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore Macchina virtuale. Per gestire le operazioni di Site Recovery in un insieme di credenziali, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore di Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Nel portale di Azure fare clic su **+Crea una risorsa** e quindi cercare **Servizi di ripristino** nel Marketplace.
2. Fare clic su **Backup e Site Recovery (OMS)**, quindi nella pagina Backup e Site Recovery fare clic su **Crea**. 
1. Nell'**insieme di credenziali di Servizi di ripristino** > **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. Per questo set di esercitazioni viene usato **ContosoVMVault**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo. Per questa esercitazione, selezionare **contosoRG**.
3. In **Località** selezionare l'area geografica in cui dovrà essere collocato l'insieme di credenziali. Viene usato **Europa occidentale**.
4. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

   ![Creare un nuovo insieme di credenziali](./media/tutorial-prepare-azure/new-vault-settings.png)

   Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create da dischi gestiti dopo il failover vengono aggiunte a questa rete.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** >  **Rete** > **Rete virtuale**.
2. Lasciare selezionato **Resource Manager** come modello di distribuzione.
3. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. In questa esercitazione viene usato **ContosoASRnet**.
4. Specificare il gruppo di risorse in cui verrà creata la rete. Usare il gruppo di risorse esistente **contosoRG**.
5. In **Intervallo di indirizzi** specificare l'intervallo **10.0.0.0/24**. In questa rete non viene usata una subnet.
6. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.
7. In **Posizione** selezionare **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
8. Lasciare le opzioni predefinite per la protezione di base DDoS, senza endpoint di servizio nella rete.
9. Fare clic su **Create**(Crea).

   ![Crea rete virtuale](media/tutorial-prepare-azure/create-network.png)

   La creazione della rete virtuale richiede qualche secondo. La rete creata viene visualizzata nel dashboard del portale di Azure.

## <a name="useful-links"></a>Collegamenti utili

- [Informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sulle reti di Azure.
- [Informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) sui dischi gestiti.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Preparare l'infrastruttura VMware locale per il ripristino di emergenza in Azure](tutorial-prepare-on-premises-vmware.md)
