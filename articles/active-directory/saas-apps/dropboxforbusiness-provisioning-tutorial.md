---
title: 'Esercitazione: configurare Dropbox for business per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Dropbox for business.
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
ms.openlocfilehash: a8aaa82a3913bcafbe8f86b85d63c39d44f08ac9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323757"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dropbox for Business per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Dropbox for business e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Dropbox for business.

> [!IMPORTANT]
> Microsoft e Dropbox definiranno la deprecazione dell'integrazione di Dropbox precedente valida 04/01/2021. Per evitare l'alterazione del servizio, è consigliabile eseguire la migrazione alla nuova integrazione di Dropbox che supporta i gruppi. Per eseguire la migrazione alla nuova integrazione con Dropbox, aggiungere e configurare una nuova istanza di Dropbox per il provisioning nel tenant di Azure AD usando la procedura seguente. Dopo aver configurato la nuova integrazione di Dropbox, disabilitare il provisioning nell'integrazione di Dropbox precedente per evitare conflitti di provisioning.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Dropbox for business](https://www.dropbox.com/business/pricing)
* Un account utente in Dropbox for business con autorizzazioni di amministratore.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Aggiungere Dropbox for business dalla raccolta

Prima di configurare Dropbox for business per il provisioning utenti automatico con Azure AD, è necessario aggiungere Dropbox for business dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Dropbox for business dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Dropbox for business**, selezionare **Dropbox for business** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Dropbox for Business nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Assegnazione di utenti in Dropbox for Business

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Dropbox for business. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Dropbox for business seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Suggerimenti importanti per l'assegnazione di utenti a Dropbox for Business

* È consigliabile assegnare un singolo Azure AD utente a Dropbox for business per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Dropbox for business, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurazione del provisioning utenti automatico in Dropbox for business 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Dropbox for business in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Dropbox for business, seguendo le istruzioni fornite nell' [esercitazione Single Sign-on su Dropbox for business](dropboxforbusiness-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Dropbox for business in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dropbox for Business**.

    ![Collegamento di Dropbox for Business nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Viene aperta una finestra di dialogo di accesso di Dropbox for Business in una nuova finestra del browser.

    ![Provisioning ](common/provisioning-oauth.png)

6. Nella finestra di dialogo **Accedi a Dropbox for business per collegarsi a Azure ad** accedere al tenant di Dropbox for business e verificare la propria identità.

    ![Accesso a Dropbox for business](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Dopo aver completato i passaggi 5 e 6, fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a Dropbox for business. Se la connessione non riesce, verificare che l'account Dropbox for business disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-oauth.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Dropbox**.

    ![Mapping utente Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Dropbox nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Dropbox per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Dropbox**.

    ![Mapping del gruppo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Dropbox nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Dropbox per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Dropbox, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Dropbox scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Dropbox.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori
 
* Dropbox non supporta la sospensione di utenti invitati. Se un utente invitato viene sospeso, l'utente verrà eliminato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

