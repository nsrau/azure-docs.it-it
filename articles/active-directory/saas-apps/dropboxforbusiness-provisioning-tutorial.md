---
title: 'Esercitazione: Configurare Dropbox for Business per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Scopri come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Dropbox for Business.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058426"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dropbox for Business per il provisioning utenti automatico

L'obiettivo di questa esercitazione è dimostrare i passaggi da eseguire in Dropbox for Business e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Dropbox for Business.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Dropbox for Business](https://www.dropbox.com/business/pricing)
* Un account utente in Dropbox for Business con autorizzazioni di amministratore.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Aggiungere Dropbox for Business dalla galleria

Prima di configurare Dropbox for Business per il provisioning automatico degli utenti con Azure AD, devi aggiungere Dropbox for Business dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Dropbox for Business dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immetti **Dropbox for Business,** seleziona **Dropbox for Business** nel riquadro dei risultati, quindi fai clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Dropbox for Business nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Assegnazione di utenti in Dropbox for Business

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, devi decidere quali utenti e/o gruppi in Azure AD devono accedere a Dropbox for Business. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Dropbox for Business seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Suggerimenti importanti per l'assegnazione di utenti a Dropbox for Business

* È consigliabile assegnare un singolo utente di Azure AD a Dropbox for Business per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando assegni un utente a Dropbox for Business, devi selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurazione del provisioning automatico degli utenti su Dropbox for Business 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Dropbox for Business in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> Puoi anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Dropbox for Business, seguendo le istruzioni fornite [nell'esercitazione sul single sign-on](dropboxforbusiness-tutorial.md)di Dropbox for Business. L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Dropbox for Business in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dropbox for Business**.

    ![Collegamento di Dropbox for Business nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Viene aperta una finestra di dialogo di accesso di Dropbox for Business in una nuova finestra del browser.

    ![Provisioning ](common/provisioning-oauth.png)

6. Nella finestra di dialogo **Accedi a Dropbox for Business per il collegamento con Azure AD** accedi al tenant di Dropbox for Business e verifica la tua identità.

    ![Accesso a Dropbox for Business](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Dopo aver completato i passaggi 5 e 6, fai clic su **Test connessione** per assicurarti che Azure AD possa connettersi a Dropbox for Business. Se la connessione non riesce, assicurati che il tuo account Dropbox for Business disponga delle autorizzazioni di amministratore e riprova.

    ![token](common/provisioning-testconnection-oauth.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Dropbox.**

    ![Mapping degli utenti Di Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Dropbox nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Dropbox per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Dropbox.**

    ![Mapping dei gruppi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Dropbox nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Dropbox per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Dropbox, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Dropbox, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definisci gli utenti e/o i gruppi di cui desideri effettuare il provisioning in Dropbox scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. Puoi usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al rapporto attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Dropbox.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Dropbox.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori
 
* Dropbox non supporta la sospensione degli utenti invitati. Se un utente invitato viene sospeso, tale utente verrà eliminato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

