---
title: Configurare la replica VMware in Azure in un ambiente multi-tenancy tramite Site Recovery e il programma Cloud Solution Provider (CSP) | Microsoft Docs
description: Descrive come creare e gestire le sottoscrizioni di tenant tramite CSP e distribuire Azure Site Recovery in una configurazione multi-tenant
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 25591acb3f046744400f5dcf20a7ea651a7bcf54
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurare la replica VMware in un ambiente multi-tenancy con il programma Cloud Solution Provider (CSP)

Il [programma CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promuove scenari di collaborazione per i servizi cloud Microsoft, tra cui Office 365, Enterprise Mobility Suite e Microsoft Azure. Con CSP, i partner acquisiscono una relazione end-to-end con i clienti e diventano il punto di contatto primario della relazione. I partner possono distribuire le sottoscrizioni di Azure per i clienti e combinarle con le proprie offerte personalizzate a valore aggiunto.

Con [Azure Site Recovery](site-recovery-overview.md), i partner possono gestire il ripristino di emergenza per i clienti direttamente tramite CSP. Possono in alternativa usare il programma CSP per configurare gli ambienti di Site Recovery e consentire ai clienti di gestire le proprie esigenze di ripristino di emergenza in modalità self-service. In entrambi gli scenari, i partner rappresentano l'anello di congiunzione tra Site Recovery e i clienti. I partner gestiscono la relazione con i clienti, addebitando loro l'uso di Site Recovery.

Questo articolo descrive come creare e gestire le sottoscrizioni di tenant tramite CSP, per uno scenario di replica VMware multi-tenant.

## <a name="prerequisites"></a>prerequisiti

Per configurare la replica VMware è necessario eseguire queste operazioni:

- [Preparare](tutorial-prepare-azure.md) le risorse di Azure, inclusi un abbonamento ad Azure, una rete virtuale di Azure e un account di archiviazione.
- [Preparare](vmware-azure-tutorial-prepare-on-premises.md) i server e le macchine virtuali VMware locali. 
- Per ogni tenant, creare un server di gestione separato che possa comunicare con le macchine virtuali tenant e i server vCenter. Solo il partner deve avere diritti di accesso a questo server di gestione. Altre informazioni sugli [ambienti multi-tenant](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Creare un account tenant

1. Accedere all'account CSP tramite il [Centro per i partner Microsoft](https://partnercenter.microsoft.com/).
2. Nel menu **Dashboard** selezionare **Clienti**.
3. Nella pagina visualizzata fare clic sul pulsante **Aggiungi cliente**.
4. Nella pagina **Nuovo cliente** compilare tutti i dettagli dell'account per il tenant. 

    ![Pagina Informazioni account](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Quindi fare clic su **Successivo: Sottoscrizioni**.
6. Nella pagina di selezione delle sottoscrizioni selezionare la casella di controllo **Microsoft Azure**. È possibile aggiungere altre sottoscrizioni subito o in qualsiasi momento nel futuro.
7. Nella pagina **Revisione** verificare i dettagli del tenant e quindi fare clic su **Invia**.
8. Dopo aver creato l'account tenant verrà visualizzata una pagina di conferma con i dettagli dell'account predefinito e la password per la sottoscrizione. Salvare le informazioni e modificare eventualmente la password in un secondo momento tramite la pagina di accesso del portale di Azure.

Queste informazioni possono essere condivise con il tenant così come sono oppure, se necessario, è possibile creare e condividere un account separato.

## <a name="access-the-tenant-account"></a>Accedere all'account tenant

È possibile accedere alla sottoscrizione del tenant tramite il dashboard del Centro per i partner Microsoft.

1. Nella pagina **Clienti** fare clic sul nome dell'account tenant.
2. Nella pagina **Sottoscrizioni** dell'account tenant è possibile monitorare le sottoscrizioni esistenti per l'account e aggiungerne altre, se necessario.
3. Per gestire le operazioni di ripristino di emergenza del tenant, selezionare l'opzione **Tutte le risorse (portale di Azure)**. Verrà così consentito l'accesso alle sottoscrizioni di Azure del tenant.

    ![Collegamento Tutte le risorse](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. È possibile verificare l'accesso facendo clic sul collegamento di Azure Active Directory nella parte superiore destra del portale di Azure.

    ![Collegamento di Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

A questo punto è possibile eseguire tutte le operazioni di Site Recovery per il tenant nel portale di Azure. Per accedere alla sottoscrizione del tenant tramite CSP per il ripristino di emergenza gestito, seguire la procedura descritta in precedenza.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuire le risorse nella sottoscrizione del tenant

1. Nel portale di Azure creare un gruppo di risorse e distribuire un insieme di credenziali di Servizi di ripristino seguendo la procedura consueta.
2. Scaricare la chiave di registrazione dell'insieme di credenziali,
3. Registrare il server di configurazione per il tenant usando la chiave di registrazione dell'insieme di credenziali.

4. Immettere le credenziali per i due account di accesso, ovvero l'account di accesso al server vCenter e l'account di accesso alla macchina virtuale.

    ![Gestire gli account del server di configurazione](./media/vmware-azure-multi-tenant-csp-disaster-recovery/config-server-account-display.png)

## <a name="register-servers-in-the-vault"></a>Registrare i server nell'insieme di credenziali

1. Passare al portale di Azure e nell'insieme di credenziali creato in precedenza registrare il server vCenter nel server di configurazione usando l'account vCenter creato. 
2. Completare il processo di preparazione dell'infrastruttura per Site Recovery seguendo la procedura consueta.
3. Le macchine virtuali sono ora pronte per essere replicate. Verificare che in **Replica** > **Seleziona macchine virtuali** siano visualizzate solo le VM del tenant.


## <a name="assign-tenant-access-to-the-subscription"></a>Assegnare l'accesso tenant alla sottoscrizione

1. Verificare che sia configurata l'infrastruttura del ripristino di emergenza. Si noti che è necessario accedere alle sottoscrizioni del tenant tramite il portale CSP, a prescindere che il ripristino di emergenza sia gestito o self-service. È necessario configurare l'insieme di credenziali e registrare l'infrastruttura nelle sottoscrizioni del tenant.
2. Fornire l'[account creato](#create-a-tenant-account) al tenant.
3. È possibile aggiungere un nuovo utente alla sottoscrizione tenant tramite il portale CSP come indicato di seguito:

    a) Accedere alla pagina delle sottoscrizioni CSP del tenant e quindi selezionare l'opzione **Users and licenses** (Utenti e licenze).

        ![The tenant's CSP subscription page](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Creare ora un nuovo utente immettendo i relativi dettagli e selezionando le autorizzazioni oppure caricando l'elenco di utenti in un file CSV.
    c) Dopo aver creato un nuovo utente, tornare al portale di Azure. Nella pagina **Sottoscrizione** selezionare la sottoscrizione da usare.
    d) Selezionare **Controllo di accesso (IAM)** e fare clic su **Aggiungi** per aggiungere un utente con il livello di accesso corrispondente. Gli utenti creati tramite il portale CSP verranno automaticamente visualizzati nella pagina che si apre dopo aver fatto clic su un livello di accesso.

        ![Add a user](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Per la maggior parte delle operazioni di gestione è sufficiente il ruolo *Collaboratore*. Gli utenti con questo livello di accesso possono eseguire qualsiasi operazione su una sottoscrizione, tranne modificare i livelli di accesso. Per questa operazione è infatti richiesto il livello di accesso *Proprietario*.
- Site Recovery ha anche tre [ruoli utente predefiniti](site-recovery-role-based-linked-access-control.md) che possono essere usati per limitare ulteriormente i livelli di accesso in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](site-recovery-role-based-linked-access-control.md) sul controllo degli accessi in base al ruolo per gestire le distribuzioni di Azure Site Recovery.
- [Altre informazioni](vmware-azure-architecture.md) sull'architettura della replica da VMware ad Azure.
- [Esaminare l'esercitazione](vmware-azure-tutorial.md) per la replica di macchine virtuali VMware in Azure.
