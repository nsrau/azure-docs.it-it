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
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.openlocfilehash: e6300932d044ec9a4f88eb5bd5977220ed11d513
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>Collegare un tenant di Office 365 a una sottoscrizione di Azure
Collegare le sottoscrizioni di Azure e Office 365 separate per accedere al tenant di Office 365 dalla sottoscrizione di Azure. Per collegare le sottoscrizioni, accedere ad Azure con l'account di amministratore del servizio di Azure, aggiungere una directory e quindi aggiungere l'account aziendale o dell'istituto di istruzione per Office 365 al tenant di Azure Active Directory.

**Si vuole trasferire una sottoscrizione di Azure esistente a un account aziendale o dell'istituto di istruzione per Office 365?** Se si è eseguita l'iscrizione ad Azure usando un account Microsoft personale e si vuole usare tale account oppure accedere con il proprio account di Office 365, è fortemente consigliabile trasferire la sottoscrizione. Vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](billing-subscription-transfer.md). 

**Si vuole eseguire l'iscrizione ad Azure con Office 365?** Vedere [Iscriversi a una sottoscrizione di Azure con il proprio account di Office 365](billing-use-existing-office-365-account-azure-subscription.md). 

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

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>Passaggio 3: Aggiungere gli account aziendali di Office 365 come amministratori della sottoscrizione di Azure
   
Per aggiungere un amministratore alla sottoscrizione di Azure, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

