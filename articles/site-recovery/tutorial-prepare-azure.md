---
title: Creare risorse da usare con Azure Site Recovery | Microsoft Docs
description: Informazioni su come preparare Azure per la replica di computer locali con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6a113169cb3f8fea1012643efcb56e5cf6c7e908
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915970"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparare le risorse di Azure per la replica dei computer locali

 [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questo articolo è la prima esercitazione di una serie che illustra come configurare il ripristino di emergenza per macchine virtuali locali. È utile se si vogliono proteggere VM VMware locali, VM Hyper-V o server fisici.

Le esercitazioni sono progettate per illustrare il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 

Questo articolo spiega come preparare i componenti di Azure quando si vuole eseguire la replica di macchine virtuali locali (Hyper-V o VMware) o server fisici Windows/Linux in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'account Azure abbia le autorizzazioni di replica.
> * Creare un account di archiviazione di Azure Le immagini delle macchine replicate sono archiviate al suo interno.
> * Creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali contiene i metadati e le informazioni di configurazione per le macchine virtuali e altri componenti di replica.
> * Configurare una rete di Azure. Le macchine virtuali di Azure create dopo il failover vengono aggiunte a questa rete di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere nell'account di archiviazione selezionato.

Per completare queste attività, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore Macchina virtuale. Per gestire le operazioni di Site Recovery in un insieme di credenziali, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore di Site Recovery.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le immagini delle macchine replicate sono archiviate nell'archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino. In questa esercitazione si usa l'area Europa occidentale.

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Archiviazione**  > **Account di archiviazione: BLOB, File, Tabelle, Code**.
2. In **Crea account di archiviazione** immettere un nome per l'account. Per queste esercitazioni viene usato **contosovmsacct1910171607**. Il nome selezionato deve essere univoco in Azure, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole.
3. In **Modello di distribuzione** selezionare **Resource Manager**.
4. In **Tipologia account** selezionare **Archiviazione (Utilizzo generico v1)**. Non selezionare l'archivio BLOB.
5. In **Replica** selezionare l'opzione predefinita **Archiviazione con ridondanza geografica e accesso in lettura** per la ridondanza dell'archiviazione. È in corso lasciando **trasferimento necessario protetto** come **disabilitato**.
6. In **Prestazioni** selezionare **Standard** e in **Livello di accesso** scegliere l'opzione predefinita di **Accesso frequente**.
7. In **Sottoscrizione** selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.
8. In **Gruppo di risorse** immettere un nuovo gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per queste esercitazioni viene usato **ContosoRG**.
9. In **Località** selezionare la posizione geografica dell'account di archiviazione. 

   ![Creare un account di archiviazione](media/tutorial-prepare-azure/create-storageacct.png)

9. Selezionare **Crea** per creare l'account di archiviazione.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Nel portale di Azure selezionare **Crea una risorsa** > **Archiviazione** > **Backup e Site Recovery (OMS)**.
2. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali. Per questo set di esercitazioni viene usato **ContosoVMVault**.
3. In **Gruppo di risorse** viene usato **contosoRG**.
4. In **Località**. Viene usato **Europa occidentale**.
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

   ![Creare un nuovo insieme di credenziali](./media/tutorial-prepare-azure/new-vault-settings.png)

   Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create dall'archiviazione dopo il failover vengono aggiunte a questa rete.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** >  **Rete** > **Rete virtuale**.
2. Lasciare selezionato **Resource Manager** come modello di distribuzione.
3. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. In questa esercitazione viene usato **ContosoASRnet**.
4. Specificare il gruppo di risorse in cui verrà creata la rete. Usare il gruppo di risorse esistente **contosoRG**.
5. In **Intervallo di indirizzi** specificare l'intervallo **10.0.0.0/24**. In questa rete non viene usata una subnet.
6. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.
7. In **Posizione** selezionare **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
8. Lasciare le opzioni predefinite per la protezione di base DDoS, senza endpoint di servizio nella rete.
9. Fare clic su **Crea**.

   ![Crea rete virtuale](media/tutorial-prepare-azure/create-network.png)

   La creazione della rete virtuale richiede qualche secondo. La rete creata viene visualizzata nel dashboard del portale di Azure.

## <a name="useful-links"></a>Collegamenti utili

- [Informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sulle reti di Azure.
- [Informazioni](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts) sui tipi di archiviazione di Azure.
- [Informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage) sulla ridondanza dell'archiviazione e il [trasferimento sicuro](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) per l'archiviazione.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Preparare l'infrastruttura VMware locale per il ripristino di emergenza in Azure](tutorial-prepare-on-premises-vmware.md)
