---
title: 'Esercitazione: Configurare Dropbox for Business per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Dropbox for Business.
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
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672918"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dropbox for Business per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Dropbox for Business e Azure Active Directory (Azure AD) configurare Azure AD effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a Dropbox for Business.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Dropbox for Business](https://www.dropbox.com/business/pricing)
* Un account utente in Dropbox for Business con autorizzazioni di amministratore.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Aggiungere Dropbox for Business dalla raccolta

Prima di configurare Dropbox for Business per provisioning utenti automatico con Azure AD, è necessario aggiungere Dropbox for Business dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Dropbox for Business dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Dropbox for Business**, selezionare **Dropbox for Business** nel pannello dei risultati e quindi fare clic sui **Add** pulsante per aggiungere l'applicazione.

    ![Dropbox for Business nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Assegnazione di utenti in Dropbox for Business

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Dropbox for Business. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Dropbox for Business seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Suggerimenti importanti per l'assegnazione di utenti a Dropbox for Business

* È consigliabile che un singolo utente di Azure AD viene assegnato a Dropbox for Business testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Dropbox for Business, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurazione del provisioning utenti automatico a Dropbox for Business 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Dropbox for Business base alle assegnazioni utente e/o di gruppo in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on per Dropbox for Business, seguendo le istruzioni disponibili nel [Dropbox for Business single sign-on di esercitazione su](dropboxforbusiness-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Dropbox for Business in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dropbox for Business**.

    ![Collegamento di Dropbox for Business nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Viene aperta una finestra di dialogo di accesso di Dropbox for Business in una nuova finestra del browser.

    ![Provisioning ](common/provisioning-oauth.png)

6. Nel **Accedi a Dropbox for Business per il collegamento con Azure AD** finestra di dialogo Accedi per il tenant Dropbox for Business e verificare la tua identità.

    ![Dropbox per l'accesso aziendale](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Dopo aver completato i passaggi 5 e 6, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Dropbox for Business. Se la connessione non riesce, assicurarsi che di Dropbox for Business account disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-oauth.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users in Dropbox**.

    ![Mapping degli utenti a dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Dropbox nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Dropbox per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory in Dropbox**.

    ![Mapping dei gruppi di dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Dropbox nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Dropbox per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il provisioning del servizio per Dropbox AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Dropbox selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio dropbox di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori
 
* Dropbox non supporta la sospensione degli utenti invitati. Se un utente invitato viene sospesa, tale utente verrà eliminato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

