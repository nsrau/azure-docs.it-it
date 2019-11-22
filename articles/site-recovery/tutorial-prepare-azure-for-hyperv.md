---
title: Preparare Azure per il ripristino di emergenza di Hyper-V con Azure Site Recovery
description: Informazioni su come preparare Azure per il ripristino di emergenza di VM Hyper-V locali con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084181"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Preparare le risorse di Azure per il ripristino di emergenza di Hyper-V

 [Azure Site Recovery](site-recovery-overview.md) contribuisce alla continuità aziendale e al ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa esercitazione è la prima di una serie che descrive come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali.

> [!NOTE]
> Le esercitazioni sono progettate per illustrare il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per altre informazioni, vedere la sezione "Procedura" per ogni scenario corrispondente.

Questa esercitazione descrive come preparare i componenti di Azure per replicare le macchine virtuali (Hyper-V) in Azure. Si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'account Azure abbia le autorizzazioni di replica.
> * Creare un account di archiviazione di Azure in cui archiviare le immagini dei computer replicati.
> * Creare un insieme di credenziali di Servizi di ripristino in cui archiviare i metadati e le informazioni di configurazione per le macchine virtuali e altri componenti di replica.
> * Configurare una rete di Azure. Quando le VM di Azure vengono create dopo il failover, vengono aggiunte a questa rete.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="sign-in"></a>Accesso

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato un account Azure gratuito, si è amministratori di tale sottoscrizione. Se non si è l'amministratore, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere nell'account di archiviazione selezionato.

Per completare queste attività, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore Macchina virtuale. Per gestire le operazioni di Site Recovery in un insieme di credenziali, è necessario che all'account sia assegnato il ruolo predefinito Collaboratore di Site Recovery.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le immagini delle macchine replicate sono archiviate nell'archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Archiviazione**  > **Account di archiviazione: BLOB, File, Tabelle, Code**.
2. In **Crea account di archiviazione** immettere un nome per l'account.  Il nome scelto deve essere univoco in Azure, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole. Per questa esercitazione usare **contosovmsacct1910171607**.
3. In **Modello di distribuzione** selezionare **Resource Manager**.
4. In **Tipologia account** selezionare **Archiviazione (utilizzo generico v1)** . Non selezionare l'archivio BLOB.
5. In **Replica** selezionare l'opzione predefinita **Archiviazione con ridondanza geografica e accesso in lettura** per la ridondanza dell'archiviazione. Lasciare disabilitata l'impostazione "Trasferimento sicuro obbligatorio".
6. In **Prestazioni** selezionare **Standard**. Successivamente, in **Livello di accesso**, selezionare l'opzione predefinita **Accesso frequente**.
7. In **Sottoscrizione** scegliere la sottoscrizione in cui creare il nuovo account di archiviazione.
8. In **Gruppo di risorse** immettere un nuovo gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per questa esercitazione usare **ContosoRG**.
9. In **Località** scegliere la posizione geografica dell'account di archiviazione. Per questa esercitazione usare **Europa occidentale**.
10. Selezionare **Crea** per creare l'account di archiviazione.

   ![Creare un account di archiviazione](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali dei servizi di ripristino

1. Nel portale di Azure selezionare **+Crea una risorsa** e quindi cercare Servizi di ripristino in Azure Marketplace.
2. Selezionare **Backup e Site Recovery (OMS)** . Quindi, nella pagina **Backup e Site Recovery**, selezionare **Crea**.
1. Nell'**insieme di credenziali di Servizi di ripristino > Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. Per questa esercitazione usare **ContosoVMVault**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo. Per questa esercitazione usare **contosoRG**.
3. In **Località** selezionare l'area geografica in cui dovrà essere collocato l'insieme di credenziali. Per questa esercitazione usare **Europa occidentale**.
4. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

![Creare un nuovo insieme di credenziali](./media/tutorial-prepare-azure/new-vault-settings.png)

Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create dall'archiviazione dopo il failover vengono aggiunte a questa rete.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** >  **Rete** > **Rete virtuale**. Lasciare selezionato Resource Manager come modello di distribuzione.
2. In **Nome** immettere un nome di rete. Il nome deve essere univoco all'interno del gruppo di risorse di Azure. Per questa esercitazione usare **ContosoASRnet**.
3. Specificare il gruppo di risorse in cui creare la rete. Per questa esercitazione, usare il gruppo di risorse esistente **contosoRG**.
4. In **Intervallo di indirizzi** immettere **10.0.0.0/24** come intervallo per la rete. Non è presente alcuna subnet per questa rete.
5. In **Sottoscrizione** selezionare la sottoscrizione in cui creare la rete.
6. In **Posizione** scegliere **Europa occidentale**. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
7. Lasciare le opzioni predefinite per la protezione di base DDoS, senza endpoint di servizio nella rete.
8. Selezionare **Create** (Crea).

![Crea rete virtuale](media/tutorial-prepare-azure/create-network.png)

La creazione della rete virtuale richiede qualche secondo. La rete creata verrà visualizzata nel dashboard del portale di Azure.

## <a name="useful-links"></a>Collegamenti utili

Vengono fornite informazioni su:
- [Reti di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Preparare l'infrastruttura Hyper-V locale per il ripristino di emergenza in Azure](hyper-v-prepare-on-premises-tutorial.md)
