---
title: Gestire il multi-tenancy in Azure Site Recovery con il programma Cloud Solution Provider (CSP) | Documentazione Microsoft
description: Descrive come creare e gestire le sottoscrizioni di tenant tramite CSP e distribuire Azure Site Recovery in una configurazione multi-tenant
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Gestire il multi-tenancy con il programma Cloud Solution Provider (CSP)

Il [programma CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promuove scenari di collaborazione per i servizi cloud Microsoft, tra cui Office 365, Enterprise Mobility Suite e Microsoft Azure. Con CSP, i partner acquisiscono una relazione end-to-end con i clienti e diventano il punto di contatto primario della relazione. I partner possono distribuire le sottoscrizioni di Azure per i clienti e combinarle con le proprie offerte personalizzate a valore aggiunto.

Con Azure Site Recovery, i partner possono gestire la soluzione di ripristino di emergenza completa per i clienti direttamente tramite CSP. Possono in alternativa usare il programma CSP per configurare gli ambienti di Site Recovery e consentire ai clienti di gestire le proprie esigenze di ripristino di emergenza in modalità self-service. In entrambi gli scenari, i partner rappresentano l'anello di congiunzione tra Site Recovery e i clienti. I partner gestiscono la relazione con i clienti, addebitando loro l'uso di Site Recovery.

Questo articolo descrive come creare e gestire le sottoscrizioni di tenant tramite CSP, per una configurazione di VMware multi-tenant.

## <a name="prerequisites"></a>prerequisiti

- [Preparare](tutorial-prepare-azure.md) le risorse di Azure, inclusi un abbonamento ad Azure, una rete virtuale di Azure e un account di archiviazione.
- [Preparare](tutorial-prepare-on-premises-vmware.md) i server e le macchine virtuali VMware locali.
- Per ogni tenant, creare un server di gestione separato che possa comunicare con le macchine virtuali tenant e il vCenter del partner. Solo il partner dispone dei diritti di accesso a questo server. Per ulteriori informazioni sui diversi ambienti multi-tenant, vedere il materiale sussidiario di [VMware multi-tenant](site-recovery-multi-tenant-support-vmware-using-csp.md).

## <a name="create-a-tenant-account"></a>Creare un account tenant

1. Accedere all'account CSP tramite il [Centro per i partner Microsoft](https://partnercenter.microsoft.com/).

2. Nel menu **Dashboard** selezionare **Clienti**.

    ![Collegamento Clienti del Centro per i partner Microsoft](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. Nella pagina visualizzata fare clic sul pulsante **Aggiungi cliente**.

    ![Pulsante Aggiungi cliente](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Nella pagina **Nuovo cliente** compilare tutti i dettagli dell'account per il tenant e fare clic su **Successivo: Sottoscrizioni**.

    ![Pagina Informazioni account](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Nella pagina di selezione delle sottoscrizioni selezionare la casella di controllo **Microsoft Azure**. È possibile aggiungere altre sottoscrizioni subito o in qualsiasi momento nel futuro.

    ![Casella di controllo della sottoscrizione di Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Nella pagina **Revisione** verificare i dettagli del tenant e quindi fare clic su **Invia**.

    ![Pagina Revisione](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Dopo aver creato l'account tenant verrà visualizzata una pagina di conferma con i dettagli dell'account predefinito e la password per la sottoscrizione.

7. Salvare le informazioni e modificare eventualmente la password in un secondo momento tramite la pagina di accesso del portale di Azure.  

    Queste informazioni possono essere condivise con il tenant così come sono oppure, se necessario, è possibile creare e condividere un account separato.

## <a name="access-the-tenant-account"></a>Accedere all'account tenant

È possibile accedere alla sottoscrizione del tenant tramite il dashboard del Centro per i partner Microsoft, come descritto in "Passaggio 1: Creare un account tenant".

1. Passare alla pagina **Clienti** e quindi fare clic sul nome dell'account tenant.

2. Nella pagina **Sottoscrizioni** dell'account tenant è possibile monitorare le sottoscrizioni esistenti per l'account e aggiungerne altre, se necessario. Per gestire le operazioni di ripristino di emergenza del tenant, selezionare l'opzione **Tutte le risorse (portale di Azure)**.

    ![Collegamento Tutte le risorse](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Fare clic su **Tutte le risorse** per consentire l'accesso alle sottoscrizioni di Azure del tenant. È possibile verificare l'accesso facendo clic sul collegamento di Azure Active Directory nella parte superiore destra del portale di Azure.

    ![Collegamento di Azure Active Directory](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

A questo punto è possibile eseguire tutte le operazioni di ripristino sito per il tenant tramite il portale di Azure e gestire le operazioni di ripristino di emergenza. Per accedere alla sottoscrizione del tenant tramite CSP per il ripristino di emergenza gestito, seguire la procedura descritta in precedenza.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuire le risorse nella sottoscrizione del tenant
1. Nel portale di Azure creare un gruppo di risorse e distribuire un insieme di credenziali di Servizi di ripristino seguendo la procedura consueta.

2. Scaricare la chiave di registrazione dell'insieme di credenziali,

3. Registrare il server di configurazione per il tenant usando la chiave di registrazione dell'insieme di credenziali.

4. Immettere le credenziali per i due account di accesso, ovvero l'account di accesso a vCenter e l'account di accesso alla macchina virtuale.

    ![Gestire gli account del server di configurazione](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Registrare l'infrastruttura di Site Recovery nell'insieme di credenziali di Servizi di ripristino
1. Passare al portale di Azure e nell'insieme di credenziali creato in precedenza registrare il server vCenter nel server di configurazione registrato in "Passaggio 3: Distribuire le risorse nella sottoscrizione del tenant". A questo scopo usare l'account di accesso a vCenter.
2. Completare il processo di preparazione dell'infrastruttura per Site Recovery seguendo la procedura consueta.
3. Le macchine virtuali sono ora pronte per essere replicate. Verificare che nel pannello **Seleziona macchine virtuali** sotto l'opzione **Replica** siano visibili solo le macchine virtuali del tenant.

    ![Elenco di macchine virtuali tenant nel pannello Seleziona macchine virtuali](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Assegnare l'accesso tenant alla sottoscrizione

Per il ripristino di emergenza self-service, fornire al tenant i dettagli dell'account, come indicato nel passaggio 6 della sezione "Passaggio 1: Creare un account tenant". Eseguire questa operazione dopo che il partner ha configurato l'infrastruttura di ripristino di emergenza. A prescindere che il ripristino di emergenza sia gestito o self-service, i partner devono accedere alle sottoscrizioni del tenant tramite il portale CSP. I partner configurano l'insieme di credenziali di loro proprietà e registrano l'infrastruttura nelle sottoscrizioni del tenant.

I partner possono anche aggiungere un nuovo utente alla sottoscrizione del tenant tramite il portale CSP seguendo questa procedura:

1. Accedere alla pagina delle sottoscrizioni CSP del tenant e selezionare l'opzione **Users and licenses** (Utenti e licenze).

    ![Pagina delle sottoscrizioni CSP del tenant](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    È ora possibile creare un nuovo utente immettendo i relativi dettagli e selezionando le autorizzazioni oppure caricando l'elenco di utenti in un file CSV.

2. Dopo aver creato un nuovo utente, tornare al portale di Azure e selezionare la sottoscrizione corrispondente nel pannello **Sottoscrizione**.

3. Nel pannello visualizzato selezionare **Controllo di accesso (IAM)** e fare clic su **Aggiungi** per aggiungere un utente con il livello di accesso corrispondente.      
    Gli utenti creati tramite il portale CSP verranno automaticamente visualizzati nel pannello aperto dopo aver fatto clic su un livello di accesso.

    ![Aggiungere un utente](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Per la maggior parte delle operazioni di gestione è sufficiente il ruolo *Collaboratore*. Gli utenti con questo livello di accesso possono eseguire qualsiasi operazione su una sottoscrizione, tranne modificare i livelli di accesso. Per questa operazione è infatti richiesto il livello di accesso *Proprietario*.

  Azure Site Recovery ha anche tre [ruoli utente predefiniti](site-recovery-role-based-linked-access-control.md) che possono essere utilizzati per limitare ulteriormente i livelli di accesso in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi
  [Altre informazioni](site-recovery-role-based-linked-access-control.md) sul controllo degli accessi in base al ruolo per gestire le distribuzioni di Azure Site Recovery.

  [Gestire gli ambienti VMware multi-tenant](site-recovery-multi-tenant-support-vmware-using-csp.md)
