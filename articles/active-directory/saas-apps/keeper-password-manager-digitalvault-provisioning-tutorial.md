---
title: 'Esercitazione: Configurare Keeper Password Manager & Digital Vault per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057522"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Esercitazione: Configurare Keeper Password Manager & Digital Vault per il provisioning automatico degli utenti

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Keeper Password Manager & Digital Vault e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Keeper Password Manager & Digital Vault.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Un account utente in Keeper Password Manager & Digital Vault con autorizzazioni di amministratore.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiungi Keeper Password Manager & Digital Vault dalla galleria

Prima di configurare Keeper Password Manager & Digital Vault per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Keeper Password Manager & Digital Vault from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Keeper Password Manager & Digital Vault**, selezionare Keeper Password Manager & Digital **Vault** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Keeper Password Manager & Digital Vault nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Assegnazione di utenti a Keeper Password Manager & Digital Vault

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Keeper Password Manager & Digital Vault.Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Keeper Password Manager & Digital Vault. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Keeper Password Manager & Digital Vault seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Suggerimenti importanti per l'assegnazione di utenti a Keeper Password Manager & Digital Vault

* È consigliabile assegnare un singolo utente di Azure AD a Keeper Password Manager & Digital Vault per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Keeper Password Manager & Digital Vault, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurazione del provisioning automatico degli utenti su Keeper Password Manager & Digital Vault 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Keeper Password Manager & Archivio digitale in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Keeper Password Manager & Digital Vault, seguendo le istruzioni fornite [nell'esercitazione sull'accesso Single Sign-On](keeperpasswordmanager-tutorial.md)di Keeper Password Manager & Digital Vault . L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Keeper Password Manager & Digital Vault in Azure AD:To configure automatic user provisioning for Keeper Password Manager & Digital Vault in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.

    ![Collegamento di Keeper Password Manager & Digital Vault nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL **tenant** e il **token segreto** di Keeper Password Manager &'account dell'archivio digitale, come descritto nel passaggio 6.

6. Accedi a [Keeper Admin Console](https://keepersecurity.com/console/#login). Fare clic su **Amministratore** e selezionare un nodo esistente o crearne uno nuovo. Passare alla scheda **Provisioning** e selezionare **Aggiungi metodo**.

    ![Console di amministrazione Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selezionare **SCIM (System for Cross-Domain Identity Management**.

    ![Keeper Aggiungi SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Fare clic su **Crea token di provisioning**.

    ![Keeper Crea endpoint](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copiare i valori per **URL** e **Token** e incollarli in **URL tenant** e **token segreto** in Azure AD. Fare clic su **Salva** per completare la configurazione del provisioning in Keeper.

    ![Keeper Crea Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Quando si popolano i campi illustrati nel passaggio 5, fare clic su Test connessione per verificare che Azure AD possa connettersi a Keeper Password Manager & Digital Vault.Upon populating the fields shown in Step 5, click **Test Connection** to ensure Azure AD can connect to Keeper Password Manager & Digital Vault. Se la connessione non riesce, verificare che Keeper Password Manager & account Digital Vault disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Keeper Password Manager & Digital Vault**.

    ![Mapping utenti di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Keeper Password Manager & Vault digitale nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Keeper Password Manager & Digital Vault per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Di keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Keeper Password Manager & Digital Vault**.

    ![Mapping dei gruppi di custodi](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Keeper Password Manager & Digital Vault per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Keeper Password Manager & Digital Vault, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Keeper Password Manager & Digital Vault, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Keeper Password Manager & Digital Vault scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Keeper Password Manager & Digital Vault.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Keeper Password Manager & Digital Vault.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Keeper Password Manager & Digital Vault richiede **che le e-mail** e **userName** abbiano lo stesso valore di origine, poiché qualsiasi aggiornamento a uno degli attributi modificherà l'altro valore.
* Keeper Password Manager & Digital Vault non supporta le eliminazioni degli utenti, ma solo disabilitare. Gli utenti disabilitati verranno visualizzati come bloccati nell'interfaccia utente di Keeper Admin Console.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

