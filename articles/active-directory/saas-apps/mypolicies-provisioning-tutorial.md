---
title: 'Esercitazione: Configurare myPolicies per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in myPolicies.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to myPolicies.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 353da826b6e339d40a5d85bbf63caac5bf7094f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061367"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Esercitazione: Configurare myPolicies per il provisioning automatico degli utentiTutorial: Configure myPolicies for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in myPolicies e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in myPolicies.The objective of this tutorial is to demonstrate the steps to be performed in myPolicies and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users and/or groups to myPolicies.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant myPolicies](https://mypolicies.com/index.html#section10).
* Un account utente in myPolicies con autorizzazioni di amministratore.

## <a name="assigning-users-to-mypolicies"></a>Assegnazione di utenti a myPoliciesAssigning users to myPolicies

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a myPolicies. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a myPolicies seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Suggerimenti importanti per l'assegnazione di utenti a myPolicies

* È consigliabile assegnare un singolo utente di Azure AD a myPolicies per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a myPolicies, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-mypolicies-for-provisioning"></a>Configurare myPolicies per il provisioning

Prima di configurare myPolicies per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in myPolicies.

1. Contatta il tuo rappresentante **support@mypolicies.com** myPolicies per ottenere il token segreto necessario per configurare il provisioning SCIM.

2.  Salvare il valore del token fornito dal rappresentante myPolicies. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione myPolicies nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your myPolicies application in the Azure portal.

## <a name="add-mypolicies-from-the-gallery"></a>Aggiungere myPolicies dalla raccolta

Per configurare myPolicies per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere myPolicies dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere myPolicies dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add myPolicies from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **myPolicies**, selezionare **myPolicies** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![myPolicies nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Configurazione del provisioning automatico degli utenti in myPoliciesConfiguring automatic user provisioning to myPolicies 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in myPolicies in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per myPolicies , seguendo le istruzioni fornite [nell'esercitazione MyPolicies Single Sign-On](mypolicies-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per myPolicies in Azure AD:To configure automatic user provisioning for myPolicies in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **myPolicies**.

    ![Collegamento di myPolicies nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** `https://<myPoliciesCustomDomain>.mypolicies.com/scim` immettere l'URL **tenant** in cui `<myPoliciesCustomDomain>` si trova il dominio personalizzato myPolicies. Puoi recuperare il dominio del cliente myPolicies dal tuo URL.
Esempio: `<demo0-qa>`.mypolicies.com.

6. In **Token segreto**immettere il valore del token recuperato in precedenza. Fare clic su Test connessione per verificare che Azure AD possa connettersi a myPolicies.Click **Test Connection** to ensure Azure AD can connect to myPolicies. Se la connessione non riesce, assicurati che l'account myPolicies disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con myPolicies**.

    ![Mapping utente di myPolicies](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a myPolicies nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in myPolicies per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utente di myPolicies](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per myPolicies, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for myPolicies, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in myPolicies scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in myPolicies.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on myPolicies.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* myPolicies richiede sempre **userName**, **email** e **externalId**.
* myPolicies non supporta le eliminazioni rigide per gli attributi utente.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
