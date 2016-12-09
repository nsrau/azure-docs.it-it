---
title: Usare un tenant di Office 365 con una sottoscrizione di Azure | Documentazione Microsoft
description: Informazioni su come aggiungere una directory (tenant) di Office 365 a una sottoscrizione di Azure per creare l&quot;associazione.
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 36cd9eac7be2d99971d8d2e227cd4b761df82d08


---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associare un tenant di Office 365 con una sottoscrizione di Azure
In passato sono stati acquistati separatamente la sottoscrizione di Azure e l'abbonamento a Office 365 e si vuole poter accedere al tenant di Office 365 dalla sottoscrizione di Azure: è facile. Questo articolo illustra i passaggi da eseguire.

> [!NOTE]
> Questo articolo non si applica ai clienti con Contratto Enterprise.
> 
> 

## <a name="quick-guidance"></a>Indicazioni rapide
Per associare il tenant di Office 365 alla sottoscrizione di Azure, usare l'account Azure per aggiungere il tenant di Office 365 e quindi associare la sottoscrizione di Azure al tenant di Office 365.

## <a name="detailed-steps"></a>Procedura dettagliata
In questo scenario Kelley Wall è un utente che ha una sottoscrizione di Azure con l'account kelley.wall@outlook.com. Kelley ha anche un abbonamento a Office 365 con l'account kelley.wall@contoso.onmicrosoft.com. Ora Kelley vuole accedere al tenant di Office 365 con la sottoscrizione di Azure.

![Screenshot dello stato di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Screenshot degli utenti attivi nell'interfaccia di amministrazione di Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Prerequisiti
Per il corretto funzionamento dell'associazione, sono previsti i seguenti prerequisiti:

* Sono necessarie le credenziali dell'amministratore del servizio della sottoscrizione di Azure. I coamministratori non possono eseguire un subset dei passaggi.
* Sono necessarie le credenziali di un amministratore globale del tenant di Office 365.
* L'indirizzo di posta elettronica dell'amministratore del servizio deve essere incluso nel tenant di Office 365.
* L'indirizzo di posta elettronica dell'amministratore del servizio deve essere diverso da quello di un amministratore globale del tenant di Office 365.
* Se si usa un indirizzo di posta elettronica che corrisponde a un account Microsoft e a un account aziendale, modificare temporaneamente le impostazioni dell'amministratore del servizio della sottoscrizione di Azure in modo da usare un altro account Microsoft. È possibile creare un nuovo account Microsoft nella [pagina Crea account Microsoft](https://signup.live.com/).

Per modificare l'amministratore del servizio, seguire questa procedura:

1. Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions).
2. Selezionare la sottoscrizione da modificare.
3. Selezionare **Modifica i dettagli della sottoscrizione**.
   
    ![Screenshot delle informazioni sulla sottoscrizione di Azure in cui è evidenziata "Modifica i dettagli della sottoscrizione"](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.
   
    ![Screenshot della finestra di dialogo "Modifica la sottoscrizione"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associare il tenant di Office 365 con la sottoscrizione di Azure
Per associare il tenant di Office 365 con la sottoscrizione di Azure, seguire questa procedura:

1. Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore del servizio.
2. Nel riquadro sinistro selezionare **ACTIVE DIRECTORY**.
   
   ![Screenshot della voce di Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > Il tenant di Office 365 non dovrebbe essere visualizzato. Se è presente, andare al passaggio successivo.
   > 
   > 
   
   ![Screenshot della directory predefinita di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. Aggiungere il tenant di Office 365 alla sottoscrizione di Azure.
   
    a. Selezionare **NUOVA** > **DIRECTORY** > **CREAZIONE PERSONALIZZATA**.
   
    ![Screenshot di Creazione personalizzata di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. Nella pagina **Aggiungi directory** in **DIRECTORY** selezionare **Utilizza directory esistente**. Selezionare quindi **È possibile uscire ora**, quindi **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Screenshot di "Utilizza directory esistente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. Dopo la disconnessione, accedere con le credenziali dell'amministratore globale del tenant di Office 365.
   
    ![Screenshot dell'accesso come amministratore globale di Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d. Selezionare **Continua**.
   
    ![Screenshot della verifica](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    e. Selezionare **Esci ora**.
   
    ![Screenshot della disconnessione](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    f. Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore del servizio.
   
    ![Screenshot dell'accesso ad Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    g. Nel dashboard è visualizzato il tenant di Office 365.
   
    ![Screenshot del dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. Cambiare la directory associata alla sottoscrizione di Azure.
   
    a. Selezionare **Impostazioni**.
   
    ![Screenshot dell'icona delle impostazioni del portale classico di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. Selezionare la sottoscrizione di Azure e quindi **MODIFICA DIRECTORY**.
    ![Screenshot di Modifica directory per la sottoscrizione di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. Selezionare **Avanti** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Screenshot di "Modifica directory associata"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > Verrà visualizzato un avviso per indicare che tutti i coamministratori verranno rimossi.
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > Verranno rimossi anche tutti gli utenti con il ruolo [Controllo degli accessi in base al ruolo](active-directory/role-based-access-control-configure.md) e l'accesso assegnato nei gruppi di risorse esistenti. Tuttavia, il messaggio di avviso visualizzato cita solo la rimozione di coamministratori.
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d. Selezionare **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
5. Ora è possibile aggiungere gli account aziendali di Office 365 come coamministratori del tenant Azure Active Directory.
   
    a. Selezionare la scheda **AMMINISTRATORI** e quindi **AGGIUNGI**.
   
    ![Screenshot della scheda Amministratori relativa alle impostazioni nel portale di Azure classico](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. Immettere un account aziendale del tenant di Office 365, selezionare la sottoscrizione di Azure e quindi **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Screenshot della finestra di dialogo Aggiungi coamministratore di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. Tornare alla scheda **AMMINISTRATORI**. L'account aziendale viene visualizzato come coamministratore.
   
    ![Screenshot della scheda Amministratori](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. È quindi possibile testare l'accesso come coamministratore.
   
    a. Disconnettersi dal portale di gestione degli account.
   
    b. Aprire il [portale di gestione degli account](https://account.windowsazure.com/subscriptions) o il [portale di Azure](https://portal.azure.com/).
   
    c. Se la pagina di accesso di Azure include un collegamento **Accedere con il proprio account aziendale**, selezionare il collegamento. In caso contrario, ignorare questo passaggio.
   
    ![Screenshot della pagina di accesso di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d. Immettere le credenziali del coamministratore e quindi selezionare **Accedi**.
   
    ![Screenshot della pagina di accesso di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Passaggi successivi
Gli scenari correlati includono:

* È già disponibile un abbonamento a Office 365 e si è pronti per una sottoscrizione di Azure, ma si vuole usare uno o più account utente di Office 365 esistenti per la sottoscrizione di Azure.
* È disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente.

Per informazioni su come eseguire queste attività, vedere [Usare l'account Office 365 esistente con la sottoscrizione di Azure o viceversa](billing-use-existing-office-365-account-azure-subscription.md).




<!--HONumber=Nov16_HO3-->


