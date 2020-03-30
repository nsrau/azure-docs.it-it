---
title: 'Esercitazione: Configurare Oracle Fusion ERP per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Oracle Fusion ERP.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061200"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Esercitazione: Configurare Oracle Fusion ERP per il provisioning automatico degli utentiTutorial: Configure Oracle Fusion ERP for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Oracle Fusion ERP e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Oracle Fusion ERP.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni generali per l'utilizzo delle funzionalità di anteprima di Microsoft Azure, vedere [Condizioni di utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information on the general Microsoft Azure terms of use for Preview features, see Supplemental Terms of Use for Microsoft Azure Previews

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Un [tenant Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Un account utente in Oracle Fusion ERP con autorizzazioni di amministratore.

## <a name="assign-users-to-oracle-fusion-erp"></a>Assegnare utenti a Oracle Fusion ERP 
Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Oracle Fusion ERP. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Oracle Fusion ERP seguendo le istruzioni qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Suggerimenti importanti per l'assegnazione degli utenti a Oracle Fusion ERP 

 * È consigliabile assegnare un singolo utente di Azure AD a Oracle Fusion ERP per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Oracle Fusion ERP, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito sono esclusi dal provisioning.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configurare Oracle Fusion ERP per il provisioning

Prima di configurare Oracle Fusion ERP per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Oracle Fusion ERP.

1. Accedi alla console di [amministrazione di Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Fare clic sul Navigatore nell'angolo in alto a sinistra. In **Strumenti**selezionare **Console di sicurezza**.

    ![Oracle Fusion ERP Aggiungi SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Passare a **Utenti**.
    
    ![Oracle Fusion ERP Aggiungi SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Salvare il nome utente e la password per l'account utente amministratore che verrà utilizzato per accedere alla console di amministrazione Oracle Fusion ERP. Questi valori devono essere immessi nei campi Nome utente e Password amministratore nella scheda Provisioning dell'applicazione Oracle Fusion ERP nel portale di Azure.These values need to be entered in the **Admin Username** and **Password** fields in the Provisioning tab of your Oracle Fusion ERP application in the Azure portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Aggiungere Oracle Fusion ERP dalla galleria

Per configurare Oracle Fusion ERP per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Oracle Fusion ERP dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Oracle Fusion ERP dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Oracle Fusion ERP from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Oracle Fusion ERP**, selezionare **Oracle Fusion ERP** nel pannello dei risultati.

    ![Oracle Fusion ERP nell'elenco dei risultati](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configurare il provisioning automatico degli utenti su Oracle Fusion ERP 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Oracle Fusion ERP in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Oracle Fusion ERP seguendo le istruzioni fornite [nell'esercitazione sul single sign-on Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Oracle Fusion ERP, vedere [API REST per le funzionalità comuni in Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Fuze in Azure AD:To configure automatic user provisioning for Fuze in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Oracle Fusion ERP**.

    ![Collegamento di Oracle Fusion ERP nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` amministratore immettere **l'input**in URL tenant. Immettere il nome utente e la password dell'amministratore recuperati in precedenza nei campi **Nome utente** amministratore e **Password.** Fare clic su **Test connessione** tra Azure AD e Oracle Fusion ERP. 

    ![Oracle Fusion ERP Aggiungi SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Oracle Fusion ERP**.

    ![Oracle Fusion ERP Aggiungi SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Oracle Fusion ERP nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Oracle Fusion ERP per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Oracle Fusion ERP Aggiungi SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Oracle Fusion ERP**.

    ![Mappature dei gruppi Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Oracle Fusion ERP nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Oracle Fusion ERP per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Oracle Fusion ERP, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Oracle Fusion ERP, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Oracle Fusion ERP scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Oracle Fusion ERP.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Oracle Fusion ERP supporta solo l'autenticazione di base per il proprio endpoint SCIM.
* Oracle Fusion ERP non supporta il provisioning dei gruppi.
* I ruoli in Oracle Fusion ERP vengono mappati ai gruppi in Azure AD. Per assegnare ruoli agli utenti in Oracle Fusion ERP da Azure AD, è necessario assegnare gli utenti ai gruppi di Azure AD desiderati denominati in base ai ruoli in Oracle Fusion ERP.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
