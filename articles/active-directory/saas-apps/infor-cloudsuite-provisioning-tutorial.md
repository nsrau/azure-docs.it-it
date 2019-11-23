---
title: 'Tutorial: Configure Infor CloudSuite for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408716"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Infor CloudSuite and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Infor CloudSuite.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [A Infor CloudSuite tenant](https://www.infor.com/products/infor-os)
* A user account in Infor CloudSuite with Admin permissions.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assigning users to Infor CloudSuite

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Infor CloudSuite. Once decided, you can assign these users and/or groups to Infor CloudSuite by following the instructions here:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Important tips for assigning users to Infor CloudSuite

* It is recommended that a single Azure AD user is assigned to Infor CloudSuite to test the automatic user provisioning configuration. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* When assigning a user to Infor CloudSuite, you must select any valid application-specific role (if available) in the assignment dialog. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Set up Infor CloudSuite for provisioning

1. Sign in to your [Infor CloudSuite Admin Console](https://www.infor.com/customer-center). Click on the user icon and then navigate to **user management**.

    ![Infor CloudSuite Admin Console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Click on the menu icon on the left top corner of the screen. Click on **Manage**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigate to **SCIM Accounts**.

    ![Infor CloudSuite SCIM Account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Add an admin user by clicking on the plus icon. Provide a **SCIM Password** and type the same password under **Confirm Password**. Click on the folder icon to save the password. You will then see an **User Identifier** generated for the admin user.

    ![Infor CloudSuite Admin user](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite password](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. To generate the bearer token, copy the **User Identifier** and **SCIM Password**. Paste them in notepad++ separated by a colon. Encode the string value by navigating to **Plugins > MIME Tools > Basic64 Encode**. 

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copy the bearer token. This value will be entered in the Secret Token field in the Provisioning tab of your Infor CloudSuite application in the Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Add Infor CloudSuite from the gallery

Before configuring Infor CloudSuite for automatic user provisioning with Azure AD, you need to add Infor CloudSuite from the Azure AD application gallery to your list of managed SaaS applications.

**To add Infor CloudSuite from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)** , in the left navigation panel, select **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. In the search box, enter **Infor CloudSuite**, select **Infor CloudSuite** in the results panel, and then click the **Add** button to add the application.

    ![Infor CloudSuite nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuring automatic user provisioning to Infor CloudSuite 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Infor CloudSuite based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for Infor CloudSuite , following the instructions provided in the [Infor CloudSuite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> To learn more about Infor CloudSuite's SCIM endpoint, refer [this](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>To configure automatic user provisioning for Infor CloudSuite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Infor CloudSuite**.

    ![Collegamento di Infor CloudSuite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning tab](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning tab](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **Tenant URL**. Input the bearer token value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Infor CloudSuite. If the connection fails, ensure your Infor CloudSuite account has Admin permissions and try again.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Review the user attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Infor CloudSuite for update operations. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Infor CloudSuite User Attributes](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Infor CloudSuite**.

    ![Infor CloudSuite Group Mappings](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Review the group attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Infor CloudSuite for update operations. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for Infor CloudSuite, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Infor CloudSuite by choosing the desired values in **Scope** in the **Settings** section.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Infor CloudSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)