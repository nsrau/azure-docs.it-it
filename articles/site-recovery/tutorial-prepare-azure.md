---
title: Creare risorse da usare con Azure Site Recovery | Microsoft Docs
description: Informazioni su come preparare Azure per la replica di computer locali con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2f6ff1d30eef1fe34e55457d9bdd4295804ec16a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparare le risorse di Azure per la replica dei computer locali

 [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa esercitazione spiega come preparare i componenti di Azure quando si vuole eseguire la replica di macchine virtuali locali (Hyper-V o VMware) o server fisici Windows/Linux in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'account abbia le autorizzazioni di replica.
> * Creare un account di archiviazione di Azure
> * Impostare una rete di Azure. Le macchine virtuali di Azure create dopo il failover vengono aggiunte a questa rete di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere nell'account di archiviazione selezionato.

Il ruolo predefinito Collaboratore Macchina virtuale ha le autorizzazioni elencate. È necessario anche avere l'autorizzazione per la gestione delle operazioni di Site Recovery. Il ruolo Collaboratore di Site Recovery ha tutte le autorizzazioni necessarie per la gestione delle operazioni di Site Recovery in un insieme di credenziali dei servizi di ripristino.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le immagini delle macchine replicate sono archiviate nell'archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure.

1. Nel menu [Portale di Azure](https://portal.azure.com) selezionare **Nuovo** > **Risorsa di archiviazione** > **Account di archiviazione**.
2. In **Crea account di archiviazione** immettere un nome per l'account. Per queste esercitazioni usare il nome **contosovmsacct1910171607**. Il nome deve essere univoco in Azure, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole.
3. In **Modello di distribuzione** selezionare **Resource Manager**.
4. In **Tipologia account** selezionare **Utilizzo generico**. In **Prestazioni** selezionare **Standard**. Non selezionare l'archivio BLOB.
5. In **Replica** selezionare l'opzione predefinita **Archiviazione con ridondanza geografica e accesso in lettura** per la ridondanza dell'archiviazione.
6. In **Sottoscrizione** selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.
7. In **Gruppo di risorse** immettere un nuovo gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per queste esercitazioni usare il nome **ContosoRG**.
8. In **Località** selezionare la posizione geografica dell'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino. Per queste esercitazioni usare l'area **Europa occidentale**.

   ![Creare un account di archiviazione](media/tutorial-prepare-azure/create-storageacct.png)

9. Selezionare **Crea** per creare l'account di archiviazione.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Nel portale di Azure selezionare **Crea una risorsa** > **Monitoraggio e gestione** > **Backup e Site Recovery**.
2. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali. Per questa esercitazione usare **ContosoVMVault**.
3. In **Gruppo di risorse** selezionare il gruppo di risorse esistente denominato **contosoRG**.
4. In **Località** specificare l'area di Azure **Europa Occidentale** che viene usata in questo set di esercitazioni.
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

   ![Creare un nuovo insieme di credenziali](./media/tutorial-prepare-azure/new-vault-settings.png)

   Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create dall'archiviazione dopo il failover vengono aggiunte a questa rete.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** >  **Rete** > **Rete virtuale**.
2. Lasciare selezionato **Resource Manager** come modello di distribuzione. Resource Manager è il modello di distribuzione consigliato. Eseguire quindi la procedura seguente:

   a. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. Usare il nome **ContosoASRnet**.

   b. In **Gruppo di risorse** usare il gruppo di risorse esistente **contosoRG**.

   c. In **Intervallo di indirizzi** specificare l'intervallo di indirizzi di rete**10.0.0.0/24**.

   d. Per questa esercitazione non è necessaria alcuna subnet.

   e. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.

   f. In **Posizione** selezionare **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.

3. Selezionare **Create**.

   ![Crea rete virtuale](media/tutorial-prepare-azure/create-network.png)

   La creazione della rete virtuale richiede qualche secondo. La rete creata viene visualizzata nel dashboard del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Preparare l'infrastruttura VMware locale per il ripristino di emergenza in Azure](tutorial-prepare-on-premises-vmware.md)
