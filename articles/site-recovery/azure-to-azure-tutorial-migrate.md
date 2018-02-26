---
title: Eseguire la migrazione di macchine virtuali di Azure tra aree di Azure con Azure Site Recovery | Microsoft Docs
description: Usare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un'area di Azure a un'altra.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.openlocfilehash: cb815f7d9c0556efcce58b53d6037e3fc8ed9c78
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="migrate-azure-vms-to-another-region"></a>Eseguire la migrazione di VM di Azure a un'altra area

Oltre a usare il servizio [Azure Site Recovery](site-recovery-overview.md) per gestire e orchestrare il ripristino di emergenza di computer locali e di macchine virtuali di Azure ai fini della continuità aziendale e del ripristino di emergenza (BCDR), è anche possibile usare Site Recovery per gestire la migrazione di macchine virtuali di Azure in un'area secondaria. Per eseguire la migrazione di macchine virtuali di Azure, è necessario abilitare la replica ed eseguirne il failover dall'area primaria all'area secondaria scelta.

Questa esercitazione illustra come eseguire la migrazione di macchine virtuali di Azure a un'altra area. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali di Servizi di ripristino
> * Abilitare la replica per una macchina virtuale
> * Eseguire un failover per la migrazione della macchina virtuale

Per questa esercitazione si presuppone che sia già disponibile una sottoscrizione di Azure. In caso contrario, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

>[!NOTE]
>
> La funzionalità di replica di Site Recovery per le macchine virtuali di Azure è attualmente disponibile in anteprima.



## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessarie macchine virtuali di Azure un'area di Azure da cui si vuole eseguire la migrazione. Prima di iniziare è inoltre necessario verificare un certo numero di impostazioni.


### <a name="verify-target-resources"></a>Verificare le risorse di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Per la macchina virtuale di destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina possibile.


### <a name="verify-account-permissions"></a>Verificare le autorizzazioni dell'account

Se è appena stato creato l'account gratuito di Azure, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una nuova macchina virtuale, è necessario quanto segue:

1. Autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Al ruolo predefinito 'Collaboratore Macchina virtuale' sono assegnate le autorizzazioni seguenti, che includono:
    - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
    - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
    - Autorizzazione per la scrittura nell'account di archiviazione selezionato

2. È necessario anche avere l'autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo 'Collaboratore di Site Recovery' ha tutte le autorizzazioni necessarie per la gestione delle operazioni di Site Recovery in un insieme di credenziali dei servizi di ripristino.


### <a name="verify-vm-outbound-access"></a>Verificare l'accesso in uscita delle macchine virtuali

1. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali di cui eseguire la migrazione. 
2. Ai fini di questa esercitazione si presuppone che le macchine virtuali di cui eseguire la migrazione possano accedere a Internet e che non venga usato un proxy firewall per controllare l'accesso in uscita. Se si usa il proxy firewall, verificare i requisiti [qui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Verificare i certificati delle macchine virtuali

Controllare che nelle macchine virtuali di Azure di cui eseguire la migrazione siano presenti tutti i certificati radice più recenti. Se i certificati radice più recenti non sono disponibili, per motivi di sicurezza non è possibile registrare la macchina virtuale in Site Recovery.

- Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.



## <a name="create-a-vault"></a>Creare un insieme di credenziali

Creare l'insieme di credenziali in tutte le aree, ad eccezione dell'area di origine.

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Monitoraggio e gestione** > **Backup e Site Recovery**.
3. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Nuovo insieme di credenziali](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.






## <a name="select-the-source"></a>Selezionare l'origine

1. In Insiemi di credenziali dei servizi di ripristino fare clic su **ContosoVMVault** > **+Replica**.
2. In **Origine** selezionare **Azure - ANTEPRIMA**.
3. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
4. Selezionare il modello di distribuzione Resource Manager. Selezionare quindi il **gruppo di risorse di origine**.
5. Fare clic su **OK** per salvare le impostazioni.


## <a name="enable-replication-for-azure-vms"></a>Abilitare la replica per le macchine virtuali di Azure

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse.


1. Nel portale di Azure fare clic su **Macchine virtuali**.
2. Selezionare la macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **OK**.
3. In **Impostazioni** fare clic su **Ripristino di emergenza (anteprima)**.
4. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
5. Per questa esercitazione accettare le altre impostazioni predefinite.
6. Fare clic su **Abilitazione della replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

    ![abilitare la replica](media/tutorial-migrate-azure-to-azure/settings.png)

>[!NOTE]
  >
  > La replica di macchine virtuali di Azure con dischi gestiti non è attualmente supportata. 

## <a name="run-a-failover"></a>Eseguire un failover

1. In **Impostazioni** > **Elementi replicati** fare clic sul computer e quindi scegliere **Failover**.
2. In **Failover** selezionare **Più recente**. L'impostazione della chiave di crittografia non è rilevante per questo scenario.
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.
5. In **Elementi replicati** fare clic con il pulsante destro del mouse su macchina virtuale > **Completa la migrazione**. Il processo di migrazione viene completato e la replica per la macchina virtuale viene arrestata.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata eseguita la migrazione di una macchina virtuale di Azure in un'area diversa di Azure. Ora è possibile configurare il ripristino di emergenza per la macchina virtuale di cui è stata eseguita la migrazione.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)

