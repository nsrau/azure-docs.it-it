---
title: Usare un tenant di Office 365 con una sottoscrizione di Azure | Documentazione Microsoft
description: Informazioni su come aggiungere una directory (tenant) di Office 365 a una sottoscrizione di Azure.
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.contentlocale: it-it
ms.lasthandoff: 02/22/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>Associare un tenant di Office 365 a una sottoscrizione di Azure
Collegare le sottoscrizioni di Azure e Office 365 separate per accedere al tenant di Office 365 dalla sottoscrizione di Azure. Per collegare le sottoscrizioni, accedere ad Azure con l'account di amministratore del servizio di Azure, aggiungere una directory e quindi l'account aziendale per Office 365 al tenant di Azure Active Directory.

Se si desidera una sottoscrizione di Office 365 per gli utenti nell'istanza di Azure Active Directory o si dispone di un account Office 365 ma non di un account Azure, vedere [Sign up for Azure with Office 365 account](billing-use-existing-office-365-account-azure-subscription.md) (Iscriversi ad Azure con un account Office 365). 

## <a name="before-you-begin"></a>Prima di iniziare
* Sono necessarie le credenziali dell'amministratore del servizio della sottoscrizione di Azure. Gli account coamministratore non possono eseguire alcuni passaggi di questo articolo. Per modificare l'amministratore dell'account, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Sono necessarie le credenziali di un amministratore globale del tenant di Office 365.
* L'indirizzo di posta elettronica dell'amministratore del servizio deve essere incluso nel tenant di Office 365.
* L'indirizzo di posta elettronica dell'amministratore del servizio deve essere diverso da quello di un amministratore globale del tenant di Office 365.
* Se si usa un indirizzo di posta elettronica che corrisponde a un account Microsoft e a un account aziendale, modificare temporaneamente le impostazioni dell'amministratore del servizio della sottoscrizione di Azure in modo da usare un altro account Microsoft. È possibile creare un account Microsoft nella [pagina Crea account Microsoft](https://signup.live.com/).

## <a name="link-office-365-tenant-to-azure-subscription"></a>Collegare il tenant di Office 365 alla sottoscrizione di Azure
Per associare il tenant di Office 365 alla sottoscrizione di Azure, seguire questa procedura:

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>Passaggio 1: Aggiungere il tenant di Office 365 alla sottoscrizione di Azure

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con le credenziali di amministratore del servizio.

    ![Screenshot dell'accesso ad Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. Nel riquadro sinistro selezionare **ACTIVE DIRECTORY**. Il tenant di Office 365 non dovrebbe essere visualizzato. Se viene visualizzato, procedere al [Passaggio 2: Cambiare la directory associata alla sottoscrizione di Azure](#Step2).
   
   ![Screenshot della voce di Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. Selezionare **NUOVA** > **DIRECTORY** > **CREAZIONE PERSONALIZZATA**.
   
    ![Screenshot di Creazione personalizzata di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. Nella pagina **Aggiungi directory** in **DIRECTORY** selezionare **Utilizza directory esistente**. Selezionare quindi **È possibile uscire ora**, quindi **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Screenshot di "Utilizza directory esistente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. Dopo la disconnessione, accedere con le credenziali dell'amministratore globale del tenant di Office 365.
   
    ![Screenshot dell'accesso come amministratore globale di Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. Selezionare **Continua**.
   
    ![Screenshot della verifica](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. Selezionare **Esci ora**.
   
    ![Screenshot della disconnessione](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con le credenziali di amministratore del servizio.
   
    ![Screenshot dell'accesso ad Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. Nel dashboard è visualizzato il tenant di Office 365.
   
    ![Screenshot del dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Passaggio 2: Cambiare la directory associata alla sottoscrizione di Azure
   
1. Selezionare **Impostazioni**.
   
    ![Screenshot dell'icona delle impostazioni del portale classico di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. Selezionare la sottoscrizione di Azure e quindi **MODIFICA DIRECTORY**.

    ![Screenshot di Modifica directory per la sottoscrizione di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. Selezionare **Avanti** ![icona Avanti](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Screenshot di "Modifica directory associata"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. Esaminare gli account interessati. Verranno rimossi tutti gli account coamministratore e gli utenti con il ruolo [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) con l'accesso assegnato nei gruppi di risorse esistenti. Il messaggio di avviso visualizzato cita solo la rimozione di coamministratori.
      
    ![Schermata che mostra gli account coamministratore da rimuovere.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Schermata che mostra un account utente di esempio da rimuovere.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. Selezionare **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>Passaggio 3: Aggiungere gli account aziendali di Office 365 come coamministratori del tenant Azure Active Directory
   
1. Selezionare la scheda **AMMINISTRATORI** e quindi **AGGIUNGI**.
   
    ![Screenshot della scheda Amministratori relativa alle impostazioni nel portale di Azure classico](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. Immettere un account aziendale del tenant di Office 365, selezionare la sottoscrizione di Azure e quindi **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Screenshot della finestra di dialogo Aggiungi coamministratore di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. Tornare alla scheda **AMMINISTRATORI**. L'account aziendale viene visualizzato come coamministratore.
   
    ![Screenshot della scheda Amministratori](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  Testare l'accesso ad Azure con l'account coamministratore.
   
    a. Disconnettersi dal portale di Azure classico.
   
    b. Aprire il [portale di Azure](https://portal.azure.com/).
   
    c. Immettere le credenziali del coamministratore e quindi selezionare **Accedi**.
   
    ![Screenshot della pagina di accesso di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.



